# Apple Wallet (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Wallet's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets for the signature card-stack expand.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces
  canvas:        '#000000',  // TRUE BLACK — not #1C1C1E
  surface1:      '#1C1C1E',
  surface2:      '#2C2C2E',
  glass:         'rgba(255,255,255,0.12)',
  hairline:      '#262629',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#A0A0A5',
  textTertiary:  '#636368',

  // Apple Card titanium gradient stops
  titaniumHi:    '#E8E8EB',
  titaniumMid:   '#A8A8AD',
  titaniumLo:    '#3D3D3F',
  chipGold:      '#C7AC73',
  dailyCash:     '#FF3B30',

  // Semantic (HIG dark mode)
  systemBlue:    '#0A84FF',
  success:       '#30D158',
  warning:       '#FF9F0A',
  error:         '#FF453A',

  // Brand hints
  chaseBlue:     '#1A2D4F',
  amexSilver:    '#A7B0B7',
  visaNavy:      '#1A1F71',
} as const;

export type WalletColor = keyof typeof colors;
```

## 2. Typography

SF Pro ships with iOS — no font loading needed. On Android, fall back to `Roboto`. The dual axis is critical: Display ≥ 20pt, Text ≤ 17pt.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
import { Platform } from 'react-native';

const sysDisplay = Platform.select({ ios: 'SF Pro Display', default: 'System' });
const sysText    = Platform.select({ ios: 'SF Pro Text',    default: 'System' });
const sysMono    = Platform.select({ ios: 'SF Mono',        default: 'monospace' });

export const typography = {
  title:        { fontFamily: sysDisplay, fontSize: 34, fontWeight: '700', lineHeight: 38, letterSpacing: 0.36 },
  sheetTitle:   { fontFamily: sysDisplay, fontSize: 28, fontWeight: '700', lineHeight: 32, letterSpacing: 0.35 },
  cardIssuer:   { fontFamily: sysDisplay, fontSize: 17, fontWeight: '600' },
  balanceHero:  { fontFamily: sysDisplay, fontSize: 40, fontWeight: '700', fontVariant: ['tabular-nums'] },
  dailyCash:    { fontFamily: sysDisplay, fontSize: 22, fontWeight: '700', fontVariant: ['tabular-nums'] },

  body:         { fontFamily: sysText, fontSize: 17, fontWeight: '400', lineHeight: 22, letterSpacing: -0.4 },
  bodyMedium:   { fontFamily: sysText, fontSize: 17, fontWeight: '500', fontVariant: ['tabular-nums'] },
  action:       { fontFamily: sysText, fontSize: 17, fontWeight: '600', letterSpacing: -0.4 },
  sectionHdr:   { fontFamily: sysText, fontSize: 13, fontWeight: '600', textTransform: 'uppercase', letterSpacing: -0.08 },
  footnote:     { fontFamily: sysText, fontSize: 13, fontWeight: '400', letterSpacing: -0.08 },
  caption:      { fontFamily: sysText, fontSize: 11, fontWeight: '400', letterSpacing: 0.07 },

  cardLast4:    { fontFamily: sysMono, fontSize: 17, fontWeight: '500', letterSpacing: 0.5, fontVariant: ['tabular-nums'] },
  cardHolder:   { fontFamily: sysText, fontSize: 13, fontWeight: '500', letterSpacing: 0.3 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Apple Card Face (the signature object)

```tsx
// components/AppleCardFace.tsx
import { View, Text, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AppleCardFace({ cardholder = 'MELIWAT' }: { cardholder?: string }) {
  return (
    <View style={styles.shadow}>
      <View style={styles.frame}>
        <LinearGradient
          colors={[colors.titaniumHi, colors.titaniumMid, colors.titaniumLo]}
          start={{ x: 0, y: 0 }}
          end={{ x: 1, y: 1 }}
          style={StyleSheet.absoluteFill}
        />
        <View style={styles.content}>
          <View style={styles.topRow}>
            <View style={styles.chip} />
            <Ionicons name="logo-apple" size={24} color="#FFFFFF" />
          </View>
          <View style={styles.bottomRow}>
            <Text style={[typography.cardHolder, { color: '#1A1A1A' }]}>
              {cardholder.toUpperCase()}
            </Text>
            <View style={styles.dailyCashChip}>
              <Text style={{ color: '#FFFFFF', fontWeight: '700', fontSize: 16 }}>$</Text>
            </View>
          </View>
        </View>
        <View style={styles.innerHighlight} pointerEvents="none" />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  shadow: {
    shadowColor: '#000', shadowOpacity: 0.5, shadowRadius: 12, shadowOffset: { width: 0, height: 8 },
    elevation: 12, marginHorizontal: 16,
  },
  frame: {
    height: 220, borderRadius: 10, overflow: 'hidden', backgroundColor: '#000',
  },
  content: {
    flex: 1, padding: 16, justifyContent: 'space-between',
  },
  topRow: { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'flex-start' },
  bottomRow: { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'flex-end' },
  chip: { width: 26, height: 20, borderRadius: 4, backgroundColor: colors.chipGold },
  dailyCashChip: {
    width: 28, height: 28, borderRadius: 14, backgroundColor: colors.dailyCash,
    alignItems: 'center', justifyContent: 'center',
  },
  innerHighlight: {
    position: 'absolute', top: 0, left: 0, right: 0, height: 0.5, backgroundColor: 'rgba(255,255,255,0.12)',
  },
});
```

### Generic Credit/Debit Card

```tsx
// components/CreditCardFace.tsx
import { View, Text, Image, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  issuer: string;
  last4: string;
  cardholder: string;
  network: 'visa' | 'mastercard' | 'amex';
  gradient: [string, string];
};

export function CreditCardFace({ issuer, last4, cardholder, network, gradient }: Props) {
  return (
    <View style={styles.shadow}>
      <View style={styles.frame}>
        <LinearGradient colors={gradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={StyleSheet.absoluteFill} />
        <View style={styles.content}>
          <View style={styles.topRow}>
            <Text style={[typography.cardIssuer, { color: '#FFFFFF' }]}>{issuer}</Text>
            <Text style={{ color: '#FFFFFF', fontStyle: 'italic', fontWeight: '900', fontSize: 14 }}>{network.toUpperCase()}</Text>
          </View>
          <View style={styles.bottomRow}>
            <Text style={[typography.cardHolder, { color: 'rgba(255,255,255,0.9)' }]}>{cardholder.toUpperCase()}</Text>
            <Text style={[typography.cardLast4, { color: '#FFFFFF' }]}>•••• {last4}</Text>
          </View>
        </View>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  shadow: { shadowColor: '#000', shadowOpacity: 0.5, shadowRadius: 12, shadowOffset: { width: 0, height: 8 }, elevation: 12, marginHorizontal: 16 },
  frame:  { height: 220, borderRadius: 10, overflow: 'hidden' },
  content:{ flex: 1, padding: 16, justifyContent: 'space-between' },
  topRow:    { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' },
  bottomRow: { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'flex-end' },
});
```

### Card Stack (the home screen)

```tsx
// components/CardStack.tsx
import { useState, ReactNode } from 'react';
import { ScrollView, Pressable, View, Dimensions } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

const PEEK = 80;
const CARD_HEIGHT = 220;
const SCREEN_H = Dimensions.get('window').height;

export function CardStack({ cards }: { cards: ReactNode[] }) {
  const [expandedIdx, setExpandedIdx] = useState<number | null>(null);

  return (
    <ScrollView style={{ flex: 1, backgroundColor: colors.canvas }} contentContainerStyle={{ paddingBottom: 120 }}>
      <View style={{ height: cards.length * PEEK + CARD_HEIGHT, position: 'relative', paddingTop: 16 }}>
        {cards.map((card, idx) => (
          <CardItem
            key={idx}
            idx={idx}
            isExpanded={expandedIdx === idx}
            isAnyExpanded={expandedIdx !== null}
            onPress={() => {
              Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
              setExpandedIdx((prev) => (prev === idx ? null : idx));
            }}
          >
            {card}
          </CardItem>
        ))}
      </View>
    </ScrollView>
  );
}

function CardItem({ idx, isExpanded, isAnyExpanded, onPress, children }: any) {
  const offsetY = useSharedValue(idx * PEEK);

  // When something is expanded but not us, slide off-screen
  useAnimatedReaction(() => isAnyExpanded && !isExpanded, (anyOther) => {
    offsetY.value = withTiming(anyOther ? SCREEN_H : idx * PEEK, { duration: 400 });
  });

  // When we are the expanded one, snap to top
  useAnimatedReaction(() => isExpanded, (mine) => {
    offsetY.value = withSpring(mine ? 16 : idx * PEEK, { damping: 16, mass: 0.8 });
  });

  const style = useAnimatedStyle(() => ({
    transform: [{ translateY: offsetY.value }],
    position: 'absolute', left: 0, right: 0,
    zIndex: isExpanded ? 100 : idx,
  }));

  return (
    <Animated.View style={style}>
      <Pressable onPress={onPress}>
        {children}
      </Pressable>
    </Animated.View>
  );
}
```

### Wallet Home (title + add button + stack)

```tsx
// app/index.tsx
import { View, Text, Pressable } from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import { SafeAreaView } from 'react-native-safe-area-context';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { CardStack } from '../components/CardStack';
import { AppleCardFace } from '../components/AppleCardFace';
import { CreditCardFace } from '../components/CreditCardFace';

export default function WalletHome() {
  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: colors.canvas }} edges={['top']}>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 16, paddingBottom: 8 }}>
        <Text style={[typography.title, { color: colors.textPrimary }]}>Wallet</Text>
        <Pressable
          onPress={() => {}}
          style={({ pressed }) => ({
            width: 32, height: 32, borderRadius: 16, backgroundColor: colors.glass,
            alignItems: 'center', justifyContent: 'center', opacity: pressed ? 0.7 : 1,
          })}
        >
          <Ionicons name="add" size={18} color="#FFFFFF" />
        </Pressable>
      </View>

      <CardStack
        cards={[
          <AppleCardFace cardholder="MELIWAT" />,
          <CreditCardFace issuer="Chase Sapphire" last4="4521" cardholder="Meliwat" network="visa" gradient={[colors.chaseBlue, '#0E1B30']} />,
          <CreditCardFace issuer="American Express"   last4="1003" cardholder="Meliwat" network="amex" gradient={[colors.amexSilver, '#5A6068']} />,
        ]}
      />
    </SafeAreaView>
  );
}
```

### Transaction Row

```tsx
// components/TransactionRow.tsx
import { View, Text, StyleSheet } from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TransactionRow({ merchant, date, amount, dailyCash }: {
  merchant: string; date: string; amount: string; dailyCash?: string;
}) {
  return (
    <View style={styles.row}>
      <View style={styles.logo}>
        <Ionicons name="cart" size={14} color="#FFFFFF" />
      </View>
      <View style={{ flex: 1, marginLeft: 12 }}>
        <Text style={[typography.body, { color: '#FFFFFF' }]}>{merchant}</Text>
        <Text style={[typography.footnote, { color: colors.textSecondary, marginTop: 2 }]}>{date}</Text>
      </View>
      <View style={{ alignItems: 'flex-end' }}>
        <Text style={[typography.bodyMedium, { color: '#FFFFFF' }]}>{amount}</Text>
        {dailyCash && (
          <Text style={[typography.caption, { color: colors.dailyCash, marginTop: 2 }]}>Daily Cash {dailyCash}</Text>
        )}
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  row: {
    flexDirection: 'row', alignItems: 'center',
    paddingHorizontal: 16, paddingVertical: 12,
    backgroundColor: colors.surface1,
    borderBottomWidth: 0.5, borderBottomColor: colors.hairline,
  },
  logo: {
    width: 32, height: 32, borderRadius: 16, backgroundColor: colors.surface2,
    alignItems: 'center', justifyContent: 'center',
  },
});
```

### Action Toolbar

```tsx
// components/ActionToolbar.tsx
import { View, Pressable, StyleSheet } from 'react-native';
import { BlurView } from 'expo-blur';
import { Ionicons } from '@expo/vector-icons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function ActionToolbar() {
  return (
    <BlurView intensity={80} tint="dark" style={styles.bar}>
      <ToolbarButton icon="card" />
      <ToolbarButton icon="list" />
      <ToolbarButton icon="ellipsis-horizontal" />
    </BlurView>
  );
}

function ToolbarButton({ icon }: { icon: any }) {
  return (
    <Pressable
      onPress={() => Haptics.selectionAsync()}
      style={({ pressed }) => ([styles.btn, pressed && { opacity: 0.7 }])}
    >
      <Ionicons name={icon} size={18} color="#FFFFFF" />
    </Pressable>
  );
}

const styles = StyleSheet.create({
  bar: {
    flexDirection: 'row', justifyContent: 'space-around', alignItems: 'center',
    paddingVertical: 12, borderTopWidth: 0.5, borderTopColor: colors.hairline,
  },
  btn: {
    width: 44, height: 44, borderRadius: 22, backgroundColor: colors.glass,
    alignItems: 'center', justifyContent: 'center',
  },
});
```

### Pay Now Button

```tsx
// components/PayNowButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { typography } from '../theme/typography';

export function PayNowButton({ onPay }: { onPay: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPay(); }}
      style={({ pressed }) => ({
        height: 54, borderRadius: 14, backgroundColor: '#FFFFFF',
        alignItems: 'center', justifyContent: 'center',
        marginHorizontal: 16,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={[typography.action, { color: '#000000' }]}>Pay Now</Text>
    </Pressable>
  );
}
```

## 4. No Tab Bar — Stack-Only Navigation

```tsx
// app/_layout.tsx
import { Stack } from 'expo-router';
import { colors } from '../theme/colors';

export default function RootLayout() {
  return (
    <Stack
      screenOptions={{
        headerShown: false,
        contentStyle: { backgroundColor: colors.canvas },
        animation: 'slide_from_bottom',
        presentation: 'modal',
      }}
    >
      <Stack.Screen name="index" />
      <Stack.Screen name="card-details" />
      <Stack.Screen name="apple-pay-overlay" options={{ presentation: 'fullScreenModal' }} />
    </Stack>
  );
}
```

## 5. Motion & Haptics

```tsx
// Card tap → expand
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
offsetY.value = withSpring(16, { damping: 16, mass: 0.8 });

// Card drag-down → collapse
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Apple Pay success
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Tab scroll detents (no tab bar — selection haptic on the stack scroll)
Haptics.selectionAsync();

// Daily Cash pulse on the Apple Card
const scale = useSharedValue(1);
useEffect(() => {
  scale.value = withRepeat(
    withSequence(withTiming(1.04, { duration: 600 }), withTiming(1, { duration: 600 })),
    -1, false
  );
}, []);
```

## 6. Icon Library

| Purpose | Ionicons | Notes |
|---------|----------|-------|
| Add card "+" | `add` | 18pt bold |
| Apple logo | `logo-apple` | 24pt |
| Card details | `card` | 18pt |
| Transactions list | `list` | 18pt |
| More menu | `ellipsis-horizontal` | 18pt |
| Disclosure | `chevron-forward` | 13pt |
| Apple Pay glyph | `finger-print` (placeholder) | Use real `PKPaymentButton` in checkout |
| Success check | `checkmark-circle` | 64pt |
| Transit NFC | `wifi` (placeholder, rotate 90°) | 24pt |
| Merchant default | `cart` / `bag` | 14pt |

## 7. Platform Notes

- **True black canvas matters**: set `backgroundColor: '#000000'` on the root SafeAreaView and the ScrollView. iOS dark mode default `#1C1C1E` will kill the floating-card effect.
- **Status bar**: `<StatusBar style="light" />` — text white over the true-black canvas.
- **Safe area**: wrap in `SafeAreaView` from `react-native-safe-area-context`. The bottom inset matters less because there's no tab bar — leave 120pt of bottom padding for the last card's shadow to clear the home indicator.
- **Apple Pay**: never recreate `PKPaymentButton` in JS. Use `expo-payments-stripe` (deprecated) or `@stripe/stripe-react-native` `PaymentSheet` for real Apple Pay. The button must be system-native for App Store compliance.
- **Card shadows on Android**: React Native shadows on Android only honor `elevation`. Use `elevation: 12` for the card frame; the shadow will appear as a flat Material-style cast (lower fidelity than iOS, expected).
- **Linear gradients**: `expo-linear-gradient` honors stop positions accurately on both platforms — use 3-stop gradient for the Apple Card titanium effect.
- **Tabular numerals**: `fontVariant: ['tabular-nums']` works on iOS but is ignored on Android — use `fontFamily: 'monospace'` as a fallback for amount-heavy screens on Android.
- **Dark-only**: Wallet doesn't toggle to a light theme — keep `useColorScheme()` ignored and lock to the dark token set.
