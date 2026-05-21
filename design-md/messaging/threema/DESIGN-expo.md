# Threema (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Threema's visual language into paste-ready Expo / React Native code: a design-token module, the signature trust-level indicator, the QR / verify surface, message bubbles, and the bottom tab bar.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `expo-clipboard`, `expo-camera` (QR scan), `react-native-qrcode-svg`, and `react-native-reanimated` v3. Threema uses the **system font** (San Francisco on iOS) — no custom fonts to bundle; identity strings use the platform monospace.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceGray:    '#F4F4F6',
  surfacePressed: '#E8E8EB',
  divider:        '#E2E2E5',
  bubbleIn:       '#EBEBED',
  bubbleOut:      '#D6F0DC',

  // Surfaces (dark)
  darkCanvas:     '#121212',
  darkSurface1:   '#1C1C1E',
  darkSurface2:   '#262628',
  darkDivider:    '#2C2C2E',
  bubbleOutDark:  '#0C5E22',

  // Text
  textPrimary:    '#111113',
  textSecondary:  '#6B6B70',
  textTertiary:   '#A6A6AB',
  darkTextPrimary:   '#ECECEC',
  darkTextSecondary: '#9A9A9F',

  // Brand
  green:        '#088A29',
  greenPressed: '#066B20',
  greenBright:  '#1FA53C',
  link:         '#4FB477',

  // Trust levels (the signature roles)
  trustRed:    '#E5453A', // Level 1 — unknown
  trustOrange: '#EF8B2C', // Level 2 — server-matched
  trustGreen:  '#15A33A', // Level 3 — verified
  trustTrack:  '#D2D2D7',

  // Semantic
  error:   '#E5453A',
  success: '#15A33A',
  warning: '#EF8B2C',
} as const;

export type TrustLevel = 1 | 2 | 3;

export const trust = {
  1: { color: colors.trustRed,    label: 'Unknown' },
  2: { color: colors.trustOrange, label: 'Server-matched' },
  3: { color: colors.trustGreen,  label: 'Verified' },
} as const satisfies Record<TrustLevel, { color: string; label: string }>;

export const trustVoiceOver = (l: TrustLevel) => `Trust level: ${trust[l].label.toLowerCase()}`;
```

## 2. Typography

Threema ships **no custom typeface** — use the system font. The Threema ID + key fingerprint use the platform monospace so characters are unambiguous when read aloud during verification.

```ts
// theme/typography.ts
import { Platform, type TextStyle } from 'react-native';

const sys  = Platform.select({ ios: 'System', default: 'sans-serif' });
const mono = Platform.select({ ios: 'Menlo',  default: 'monospace'   });
const primary = { color: '#111113' } satisfies TextStyle;

export const typography = {
  largeTitle:  { ...primary, fontFamily: sys, fontSize: 32, fontWeight: '800', letterSpacing: -0.4 },
  screenTitle: { ...primary, fontFamily: sys, fontSize: 26, fontWeight: '700', letterSpacing: -0.3 },
  section:     { ...primary, fontFamily: sys, fontSize: 22, fontWeight: '700', letterSpacing: -0.2 },
  navName:     { ...primary, fontFamily: sys, fontSize: 18, fontWeight: '600', letterSpacing: -0.1 },
  body:        { ...primary, fontFamily: sys, fontSize: 16, fontWeight: '400', lineHeight: 22 },
  listTitle:   { ...primary, fontFamily: sys, fontSize: 15, fontWeight: '500' },
  secondary:   { color: '#6B6B70', fontFamily: sys, fontSize: 14, fontWeight: '400' },
  caption:     { color: '#6B6B70', fontFamily: sys, fontSize: 12, fontWeight: '500' },
  button:      { color: '#FFFFFF', fontFamily: sys, fontSize: 16, fontWeight: '600' },
  tab:         { color: '#818185', fontFamily: sys, fontSize: 10, fontWeight: '500', letterSpacing: 0.1 },
  badge:       { color: '#FFFFFF', fontFamily: sys, fontSize: 12, fontWeight: '700' },

  // Identity strings only — monospaced
  id:      { ...primary, fontFamily: mono, fontSize: 14, fontWeight: '600', letterSpacing: 1 },
  idLarge: { ...primary, fontFamily: mono, fontSize: 16, fontWeight: '700', letterSpacing: 1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Trust-Level Indicator (the core atom)

```tsx
// components/TrustDots.tsx
import { View, Text } from 'react-native';
import Animated, { useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { trust, trustVoiceOver, type TrustLevel } from '../theme/colors';
import { typography } from '../theme/typography';

export function TrustDots({
  level, dotSize = 8, showLabel = true,
}: { level: TrustLevel; dotSize?: number; showLabel?: boolean }) {
  const t = trust[level];
  return (
    <View
      accessible
      accessibilitylabel={trustVoiceOver(level)}
      style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}
    >
      <View style={{ flexDirection: 'row', gap: 3 }}>
        {[0, 1, 2].map((i) => (
          <View key={i} style={{ width: dotSize, height: dotSize, borderRadius: dotSize / 2, backgroundColor: t.color }} />
        ))}
      </View>
      {showLabel && (
        <Text style={[typography.secondary, { color: '#9A9A9F' }]}>{t.label}</Text>
      )}
    </View>
  );
}
```

### Threema ID Chip

```tsx
// components/ThreemaIdChip.tsx
import { Pressable, Text, View } from 'react-native';
import * as Clipboard from 'expo-clipboard';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ThreemaIdChip({ id }: { id: string }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 8,
      paddingHorizontal: 12, paddingVertical: 8,
      backgroundColor: colors.darkSurface2, borderRadius: 8,
    }}>
      <Text style={[typography.idLarge, { color: colors.darkTextPrimary }]}>{id}</Text>
      <Pressable hitSlop={8} onPress={() => Clipboard.setStringAsync(id)}>
        <Ionicons name="copy-outline" size={15} color={colors.greenBright} />
      </Pressable>
    </View>
  );
}
```

### QR Verify Card + Scanner

```tsx
// components/QRVerifyCard.tsx
import { View, Text } from 'react-native';
import QRCode from 'react-native-qrcode-svg';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function QRVerifyCard({ threemaID, payload }: { threemaID: string; payload: string }) {
  return (
    <View style={{ alignItems: 'center', gap: 16 }}>
      <View style={{ padding: 16, backgroundColor: '#FFFFFF', borderRadius: 12 }}>
        <QRCode value={payload} size={220} color={colors.green} backgroundColor="#FFFFFF" />
      </View>
      <Text style={[typography.idLarge, { color: colors.darkTextPrimary }]}>{threemaID}</Text>
      <Text style={[typography.secondary, { color: colors.darkTextSecondary, textAlign: 'center', paddingHorizontal: 24 }]}>
        Scan in person to reach three green dots — the only fully-verified state.
      </Text>
    </View>
  );
}

export function ScannerReticle() {
  const pulse = useSharedValue(0);
  useEffect(() => {
    pulse.value = withRepeat(withTiming(1, { duration: 1200 }), -1, true);
  }, []);
  const style = useAnimatedStyle(() => ({
    borderColor: pulse.value > 0.5 ? colors.trustGreen : '#FFFFFF',
  }));
  return (
    <Animated.View style={[{ width: 240, height: 240, borderWidth: 2.5, borderRadius: 16 }, style]} />
  );
}
```

### Message Bubble

```tsx
// components/MessageBubble.tsx
import { View, Text } from 'react-native';
import Animated, { FadeInUp } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Dir = 'in' | 'out';

export function MessageBubble({
  text, time, dir, read = false,
}: { text: string; time: string; dir: Dir; read?: boolean }) {
  const out = dir === 'out';
  return (
    <Animated.View entering={FadeInUp.duration(200)} style={{ flexDirection: 'row', justifyContent: out ? 'flex-end' : 'flex-start' }}>
      <View style={{
        maxWidth: '78%',
        paddingHorizontal: 13, paddingVertical: 8,
        backgroundColor: out ? colors.bubbleOutDark : colors.darkSurface2,
        borderRadius: 16,
        borderBottomRightRadius: out ? 4 : 16,
        borderBottomLeftRadius: out ? 16 : 4,
      }}>
        <Text style={[typography.body, { color: out ? '#EAFBEF' : colors.darkTextPrimary }]}>{text}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'flex-end', gap: 4, marginTop: 4 }}>
          <Text style={{ fontSize: 10, color: out ? 'rgba(255,255,255,0.55)' : '#7A7A7E' }}>{time}</Text>
          {out && (
            <Ionicons name="checkmark-done" size={12} color={read ? '#6FD68C' : 'rgba(255,255,255,0.55)'} />
          )}
        </View>
      </View>
    </Animated.View>
  );
}
```

### Chat Navigation Bar

```tsx
// components/ChatNavBar.tsx
import { View, Text, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, type TrustLevel, trust } from '../theme/colors';
import { typography } from '../theme/typography';
import { TrustDots } from './TrustDots';

export function ChatNavBar({
  name, threemaID, level, onBack, onCall,
}: { name: string; threemaID: string; level: TrustLevel; onBack: () => void; onCall: () => void }) {
  return (
    <View style={{
      height: 54, flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 14,
      backgroundColor: 'rgba(18,18,18,0.96)', borderBottomWidth: 0.5, borderBottomColor: colors.darkDivider,
    }}>
      <Pressable onPress={onBack} hitSlop={8}>
        <Ionicons name="chevron-back" size={24} color={colors.greenBright} />
      </Pressable>
      <View style={{
        width: 34, height: 34, borderRadius: 17, backgroundColor: colors.green,
        alignItems: 'center', justifyContent: 'center',
      }}>
        <Text style={{ color: '#FFF', fontSize: 14, fontWeight: '700' }}>{name.slice(0, 2).toUpperCase()}</Text>
      </View>
      <View style={{ flex: 1 }}>
        <Text style={[typography.navName, { color: colors.darkTextPrimary }]}>{name}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5 }}>
          <TrustDots level={level} dotSize={7} showLabel={false} />
          <Text style={{ fontSize: 11, color: colors.darkTextSecondary }}>
            {trust[level].label} · {threemaID}
          </Text>
        </View>
      </View>
      <Pressable onPress={onCall} hitSlop={8}>
        <Ionicons name="call-outline" size={21} color={colors.greenBright} />
      </Pressable>
    </View>
  );
}
```

### Primary Button + Composer

```tsx
// components/ThreemaButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ThreemaPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.greenPressed : colors.green,
        paddingVertical: 14, borderRadius: 10, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

```tsx
// components/Composer.tsx
import { View, TextInput, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function Composer({ value, onChange, onSend }: { value: string; onChange: (t: string) => void; onSend: () => void }) {
  const enabled = value.trim().length > 0;
  return (
    <View style={{ height: 56, flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 12,
      borderTopWidth: 0.5, borderTopColor: colors.darkDivider, backgroundColor: 'rgba(18,18,18,0.96)' }}>
      <Pressable hitSlop={8}><Ionicons name="add-circle-outline" size={24} color={colors.greenBright} /></Pressable>
      <TextInput
        value={value} onChangeText={onChange} placeholder="Message"
        placeholderTextColor={colors.darkTextSecondary}
        style={{ flex: 1, height: 38, borderRadius: 19, backgroundColor: colors.darkSurface2,
          paddingHorizontal: 16, color: colors.darkTextPrimary, fontSize: 14 }}
      />
      <Pressable onPress={onSend} disabled={!enabled} style={{
        width: 36, height: 36, borderRadius: 18, alignItems: 'center', justifyContent: 'center',
        backgroundColor: enabled ? colors.green : colors.darkSurface2,
      }}>
        <Ionicons name="send" size={16} color={enabled ? '#FFF' : colors.darkTextSecondary} />
      </Pressable>
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
        tabBarActiveTintColor: colors.greenBright,
        tabBarInactiveTintColor: '#818185',
        tabBarStyle: { backgroundColor: colors.darkCanvas, borderTopWidth: 0.5, borderTopColor: colors.darkDivider },
        tabBarLabelStyle: { fontSize: 10, fontWeight: '500', letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Chats',    tabBarBadge: 3, tabBarIcon: ({ color }) => <Ionicons name="chatbubble"        size={22} color={color} /> }} />
      <Tabs.Screen name="contacts" options={{ title: 'Contacts', tabBarIcon: ({ color }) => <Ionicons name="people-outline"   size={22} color={color} /> }} />
      <Tabs.Screen name="calls"    options={{ title: 'Calls',    tabBarIcon: ({ color }) => <Ionicons name="call-outline"     size={22} color={color} /> }} />
      <Tabs.Screen name="myid"     options={{ title: 'My ID',    tabBarIcon: ({ color }) => <Ionicons name="qr-code-outline"  size={22} color={color} /> }} />
      <Tabs.Screen name="settings" options={{ title: 'Settings', tabBarIcon: ({ color }) => <Ionicons name="settings-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Trust upgrade — crossfade red/orange → green on verification success
// Animate the TrustDots color with withTiming(..., { duration: 200 }) + success haptic:
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// QR reticle pulse (scanning loop)
pulse.value = withRepeat(withTiming(1, { duration: 1200 }), -1, true);

// Send message — bubble enters from below
// MessageBubble uses entering={FadeInUp.duration(200)}

// Sheet present (share ID / verify options)
// Use @gorhom/bottom-sheet or expo-router modal — 300ms slide-up, scrim fade 250ms

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);                 // send, button press
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);   // verify success
Haptics.selectionAsync();                                              // tab change
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The trust-dot indicator is drawn with plain `View` circles (not an icon); the Threema ID is monospaced text, not a glyph.

| Purpose | Ionicons |
|---------|----------|
| Chats (tab) | `chatbubble` / `chatbubble-outline` |
| Contacts (tab) | `people-outline` |
| Calls (tab) | `call-outline` |
| My ID (tab) | `qr-code-outline` |
| Settings (tab) | `settings-outline` |
| Back | `chevron-back` |
| Call (nav) | `call-outline` |
| Send | `send` |
| Attach (+) | `add-circle-outline` |
| Copy ID | `copy-outline` |
| Scan QR | `scan-outline` |
| Verified badge | `shield-checkmark` |
| Delivered / read | `checkmark-done` |
| Encrypted | `lock-closed` |
| Block / revoke | `ban` / `trash-outline` |
| Search | `search` |
| Add contact | `person-add-outline` |

## 7. Platform Notes

- **Font choice**: Threema uses the OS system font — `fontFamily: 'System'` on iOS, `'sans-serif'` on Android; identity strings (Threema ID / fingerprint) use `'Menlo'` (iOS) / `'monospace'` (Android). Do not bundle a custom typeface
- **Status bar**: `<StatusBar style="dark" />` on light mode, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; nav bar, composer, and tab bar need safe-area padding; QR scanner renders full-bleed with controls inset
- **Dynamic Type**: RN respects system scale on `<Text>`; set `allowFontScaling={false}` on tab labels, the trust-dot label, and the monospaced ID string (layout-sensitive)
- **Keyboard**: use `KeyboardAvoidingView` on the chat screen so the composer pins above the keyboard
- **Camera / QR**: use `expo-camera`'s `CameraView` with `barcodeScannerSettings={{ barcodeTypes: ['qr'] }}`; on a successful match, fire the success haptic and animate the contact to three green dots
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1`; brand green resolves to `greenBright` on dark — but trust-dot colors NEVER change between schemes
- **Accessibility**: trust state is never color-only — `TrustDots` carries `accessibilityLabel`; provide a VoiceOver equivalent for the QR scan action
- **Privacy**: the Threema ID is the only identifier — never collect or display a phone number / email as account identity
- **No analytics on identity**: do not log the Threema ID or fingerprint; treat them as sensitive strings
