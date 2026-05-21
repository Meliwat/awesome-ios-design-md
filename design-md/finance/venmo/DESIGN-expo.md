# Venmo (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Venmo's visual language into paste-ready Expo / React Native code: design tokens, the social feed row, Pay/Request split pill, balance card, and the animated payment-sent confirmation.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, `expo-haptics`, `expo-linear-gradient`, and optionally `react-native-svg` for the checkmark stroke animation.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas
  canvas:         '#FFFFFF',
  surfaceMuted:   '#F7F7F7',
  divider:        '#F2F2F2',
  dividerStrong:  '#E0E0E0',

  // Text
  textPrimary:    '#2F3033',
  textSecondary:  '#6B6E76',
  textTertiary:   '#9AA0A8',

  // Brand
  blue:           '#008CFF',
  blueDeep:       '#0078DE',
  blueLight:      '#3D95CE',
  blueTint:       '#E6F4FF',

  // Semantic
  receivedGreen:  '#4BB543',
  chargeRed:      '#D32E2E',
  pendingOrange:  '#F5A623',

  // Dark
  darkCanvas:     '#1A1A1A',
  darkSurface1:   '#242424',
  darkSurface2:   '#2E2E2E',
  darkDivider:    '#2A2A2A',
  darkTextPrim:   '#FFFFFF',
  darkTextSec:    '#A0A0A0',
} as const;

export const brandGradient = [colors.blue, colors.blueDeep] as const;
```

## 2. Typography

Venmo Sans and Venmo Display are proprietary. Fall back to Source Sans Pro (free) or System (SF Pro on iOS).

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { fontFamily: 'System' } satisfies TextStyle;

export const typography = {
  balanceDisplay:  { ...base, fontWeight: '700', fontSize: 56, lineHeight: 56, letterSpacing: -0.8, color: '#FFFFFF' },
  confirmDisplay:  { ...base, fontWeight: '700', fontSize: 44, lineHeight: 44, letterSpacing: -0.5, color: '#FFFFFF' },
  signupDisplay:   { ...base, fontWeight: '700', fontSize: 36, lineHeight: 40, letterSpacing: -0.4, color: '#FFFFFF' },
  amountEntry:     { ...base, fontWeight: '700', fontSize: 72, lineHeight: 72, letterSpacing: -1.0, color: '#2F3033' },

  screenTitle:     { ...base, fontWeight: '700', fontSize: 22, lineHeight: 26, letterSpacing: -0.2, color: '#2F3033' },
  feedName:        { ...base, fontWeight: '600', fontSize: 15, lineHeight: 21, color: '#2F3033' },
  feedBody:        { ...base, fontWeight: '400', fontSize: 15, lineHeight: 21, color: '#2F3033' },
  feedMemo:        { ...base, fontWeight: '400', fontSize: 15, lineHeight: 21, color: '#2F3033' },
  timestamp:       { ...base, fontWeight: '400', fontSize: 13, lineHeight: 17, color: '#6B6E76' },
  comment:         { ...base, fontWeight: '400', fontSize: 13, lineHeight: 18, color: '#2F3033' },
  amount:          { ...base, fontWeight: '700', fontSize: 17, lineHeight: 19, color: '#2F3033' },
  button:          { ...base, fontWeight: '700', fontSize: 16, letterSpacing: 0.2, color: '#FFFFFF' },
  buttonSmall:     { ...base, fontWeight: '600', fontSize: 15, color: '#008CFF' },
  input:           { ...base, fontWeight: '400', fontSize: 16, color: '#2F3033' },
  tab:             { ...base, fontWeight: '600', fontSize: 11, letterSpacing: 0.1, color: '#6B6E76' },
  meta:            { ...base, fontWeight: '400', fontSize: 12, color: '#6B6E76' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Feed Transaction Row

```tsx
// components/VenmoFeedRow.tsx
import React from 'react';
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function VenmoFeedRow({
  actorName, verb, recipientName, timestamp, memo, avatarUri, likeCount, commentCount,
}: {
  actorName: string; verb: string; recipientName: string;
  timestamp: string; memo: string;
  avatarUri: string; likeCount: number; commentCount: number;
}) {
  const [liked, setLiked] = React.useState(false);

  const handleLike = () => {
    setLiked(prev => !prev);
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
  };

  return (
    <View style={{ paddingHorizontal: 16, paddingVertical: 12, backgroundColor: colors.canvas, borderBottomWidth: 1, borderBottomColor: colors.divider }}>
      <View style={{ flexDirection: 'row', gap: 12 }}>
        <Image source={{ uri: avatarUri }} style={{ width: 40, height: 40, borderRadius: 20, backgroundColor: colors.surfaceMuted }} />
        <View style={{ flex: 1, gap: 4 }}>
          <Text>
            <Text style={typography.feedName}>{actorName}</Text>
            <Text style={typography.feedBody}> {verb} </Text>
            <Text style={typography.feedName}>{recipientName}</Text>
            <Text style={typography.timestamp}> · {timestamp}</Text>
          </Text>
          <Text style={typography.feedMemo} numberOfLines={3}>{memo}</Text>
        </View>
      </View>

      <View style={{ flexDirection: 'row', gap: 16, marginTop: 8, marginLeft: 52 }}>
        <Pressable onPress={handleLike} hitSlop={8} style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
          <Ionicons name={liked ? 'heart' : 'heart-outline'} size={18} color={liked ? colors.blue : colors.textSecondary} />
          <Text style={typography.comment}>{likeCount + (liked ? 1 : 0)}</Text>
        </Pressable>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
          <Ionicons name="chatbubble-outline" size={18} color={colors.textSecondary} />
          <Text style={typography.comment}>{commentCount}</Text>
        </View>
      </View>
    </View>
  );
}
```

### Pay / Request Split Pill

```tsx
// components/VenmoPayRequestPill.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function VenmoPayRequestPill({
  onPay, onRequest,
}: { onPay: () => void; onRequest: () => void }) {
  return (
    <View style={{
      flexDirection: 'row',
      height: 50, borderRadius: 25,
      marginHorizontal: 16, marginBottom: 4,
      backgroundColor: colors.blue, overflow: 'hidden',
      shadowColor: colors.blue, shadowOpacity: 0.25, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
    }}>
      <SplitHalf title="Pay" icon="arrow-up" onPress={onPay} />
      <View style={{ width: 1, backgroundColor: 'rgba(255,255,255,0.6)', marginVertical: 10 }} />
      <SplitHalf title="Request" icon="arrow-down" onPress={onRequest} />
    </View>
  );
}

function SplitHalf({ title, icon, onPress }: { title: string; icon: string; onPress: () => void }) {
  const bg = useSharedValue(0);
  const style = useAnimatedStyle(() => ({ backgroundColor: bg.value ? colors.blueDeep : 'transparent' }));
  return (
    <Pressable
      onPressIn={() => (bg.value = withTiming(1, { duration: 60 }))}
      onPressOut={() => (bg.value = withTiming(0, { duration: 120 }))}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={{ flex: 1 }}
    >
      <Animated.View style={[{
        flex: 1, flexDirection: 'row',
        alignItems: 'center', justifyContent: 'center', gap: 6,
      }, style]}>
        <Ionicons name={icon as any} size={16} color="#FFFFFF" />
        <Text style={typography.button}>{title}</Text>
      </Animated.View>
    </Pressable>
  );
}
```

### Balance Card

```tsx
// components/VenmoBalanceCard.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { brandGradient, colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function VenmoBalanceCard({
  balance, onTransfer, onAddMoney,
}: { balance: number; onTransfer: () => void; onAddMoney: () => void }) {
  return (
    <LinearGradient
      colors={brandGradient}
      start={{ x: 0, y: 0 }} end={{ x: 0, y: 1 }}
      style={{
        marginHorizontal: 16, borderRadius: 20,
        height: 180, padding: 20, justifyContent: 'space-between',
      }}
    >
      <Text style={{ ...typography.buttonSmall, color: 'rgba(255,255,255,0.8)' }}>Venmo balance</Text>
      <Text style={[typography.balanceDisplay, { fontVariant: ['tabular-nums'] }]}>
        {formatCurrency(balance)}
      </Text>
      <View style={{ flexDirection: 'row', gap: 12 }}>
        <WhitePill title="Transfer to bank" onPress={onTransfer} />
        <WhitePill title="Add money" onPress={onAddMoney} />
      </View>
    </LinearGradient>
  );
}

function WhitePill({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress}
      style={{
        paddingHorizontal: 16, paddingVertical: 10,
        borderRadius: 20, backgroundColor: colors.canvas,
      }}>
      <Text style={typography.buttonSmall}>{title}</Text>
    </Pressable>
  );
}

function formatCurrency(n: number): string {
  return new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' }).format(n);
}
```

### Amount Entry Screen

```tsx
// app/pay.tsx
import React from 'react';
import { Pressable, ScrollView, Text, TextInput, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export default function PayScreen() {
  const [cents, setCents] = React.useState(0);
  const [memo, setMemo] = React.useState('');
  const quickEmojis = ['🍕','🍺','🚗','💰','🎉','☕','🏠','🎁','🛒','✈️','🎬','💵'];

  const display = new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' })
    .format(cents / 100);

  return (
    <View style={{ flex: 1, backgroundColor: colors.canvas, padding: 16, alignItems: 'center', justifyContent: 'center' }}>
      <Text style={[typography.amountEntry, { fontVariant: ['tabular-nums'] }]}>{display}</Text>

      <TextInput
        value={memo} onChangeText={setMemo}
        placeholder="What's it for?"
        placeholderTextColor={colors.textTertiary}
        style={[typography.input, { textAlign: 'center', marginTop: 20, paddingHorizontal: 40 }]}
      />

      <ScrollView horizontal showsHorizontalScrollIndicator={false}
        contentContainerStyle={{ gap: 8, paddingHorizontal: 16, marginTop: 20 }}>
        {quickEmojis.map(e => (
          <Pressable key={e} onPress={() => setMemo(prev => prev + e)}
            style={{
              width: 40, height: 32, borderRadius: 16,
              alignItems: 'center', justifyContent: 'center',
              backgroundColor: colors.surfaceMuted,
            }}>
            <Text style={{ fontSize: 22 }}>{e}</Text>
          </Pressable>
        ))}
      </ScrollView>

      <View style={{ flex: 1 }} />

      <Pressable
        onPress={() => {/* send */}}
        style={{
          height: 52, width: '100%', borderRadius: 26,
          backgroundColor: colors.blue,
          alignItems: 'center', justifyContent: 'center',
          marginBottom: 24,
        }}>
        <Text style={typography.button}>Pay {display}</Text>
      </Pressable>
    </View>
  );
}
```

### Payment-Sent Confirmation (Animated Checkmark)

```tsx
// components/VenmoSentConfirmation.tsx
import React from 'react';
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Svg, { Path } from 'react-native-svg';
import Animated, {
  useSharedValue, useAnimatedProps, useAnimatedStyle, withSpring, withTiming, withDelay, Easing,
} from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { brandGradient, colors } from '../theme/colors';
import { typography } from '../theme/typography';

const AnimatedPath = Animated.createAnimatedComponent(Path);

export function VenmoSentConfirmation({
  amount, recipientName,
}: { amount: number; recipientName: string }) {
  const circleScale = useSharedValue(0);
  const checkOffset = useSharedValue(100); // 100 = hidden (strokeDashoffset)

  React.useEffect(() => {
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
    circleScale.value = withSpring(1.0, { damping: 10 });
    checkOffset.value = withDelay(200, withTiming(0, { duration: 350, easing: Easing.out(Easing.ease) }));
  }, []);

  const circleStyle = useAnimatedStyle(() => ({ transform: [{ scale: circleScale.value }] }));
  const pathProps = useAnimatedProps(() => ({ strokeDashoffset: checkOffset.value }));

  const display = new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' }).format(amount);

  return (
    <LinearGradient colors={brandGradient} style={{ flex: 1, alignItems: 'center', justifyContent: 'center', gap: 24 }}>
      <Animated.View style={[{
        width: 80, height: 80, borderRadius: 40,
        backgroundColor: colors.blue,
        alignItems: 'center', justifyContent: 'center',
      }, circleStyle]}>
        <Svg width={44} height={32} viewBox="0 0 44 32">
          <AnimatedPath
            d="M2 18 L16 30 L42 2"
            stroke="#FFFFFF" strokeWidth={6}
            strokeLinecap="round" strokeLinejoin="round" fill="none"
            strokeDasharray={100}
            animatedProps={pathProps}
          />
        </Svg>
      </Animated.View>

      <View style={{ alignItems: 'center', gap: 6 }}>
        <Text style={typography.signupDisplay}>Sent</Text>
        <Text style={[typography.confirmDisplay, { fontVariant: ['tabular-nums'] }]}>{display}</Text>
        <Text style={{ ...typography.input, color: 'rgba(255,255,255,0.8)' }}>To {recipientName}</Text>
      </View>

      <Pressable style={{
        paddingHorizontal: 32, paddingVertical: 12, borderRadius: 26,
        backgroundColor: '#FFFFFF',
      }}>
        <Text style={{ ...typography.buttonSmall, color: colors.blue }}>Done</Text>
      </Pressable>
    </LinearGradient>
  );
}
```

## 4. Tab Bar (with Pay Pill overlay)

```tsx
// app/(tabs)/_layout.tsx
import React from 'react';
import { View } from 'react-native';
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { VenmoPayRequestPill } from '../../components/VenmoPayRequestPill';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <View style={{ flex: 1 }}>
      <Tabs
        screenOptions={{
          tabBarActiveTintColor: colors.blue,
          tabBarInactiveTintColor: colors.textSecondary,
          tabBarStyle: { backgroundColor: colors.canvas, borderTopColor: colors.divider, borderTopWidth: 0.5 },
          tabBarLabelStyle: { fontSize: 11, fontWeight: '600' },
        }}
      >
        <Tabs.Screen name="index"     options={{ title: 'Home',       tabBarIcon: ({ color }) => <Ionicons name="home"       size={24} color={color} /> }} />
        <Tabs.Screen name="incomplete" options={{ title: 'Incomplete', tabBarIcon: ({ color }) => <Ionicons name="time"       size={24} color={color} /> }} />
        <Tabs.Screen name="spacer"    options={{ title: '',            tabBarIcon: () => null, tabBarItemStyle: { opacity: 0 } }} />
        <Tabs.Screen name="cards"     options={{ title: 'Cards',       tabBarIcon: ({ color }) => <Ionicons name="card"       size={24} color={color} /> }} />
        <Tabs.Screen name="me"        options={{ title: 'Me',          tabBarIcon: ({ color }) => <Ionicons name="person"     size={24} color={color} /> }} />
      </Tabs>

      {/* Pay / Request pill anchored over the tab bar */}
      <View style={{ position: 'absolute', left: 0, right: 0, bottom: 80 }}>
        <VenmoPayRequestPill onPay={() => {}} onRequest={() => {}} />
      </View>
    </View>
  );
}
```

## 5. Motion Specs

```tsx
// Pay/Request half press — 60ms background fade
bg.value = withTiming(1, { duration: 60 });
bg.value = withTiming(0, { duration: 120 });

// Checkmark — spring + delayed stroke draw
circleScale.value = withSpring(1.0, { damping: 10 });
checkOffset.value = withDelay(200, withTiming(0, { duration: 350 }));

// Confetti — use react-native-confetti-cannon on confirm screen mount
<ConfettiCannon count={40} origin={{ x: -10, y: 0 }} colors={[colors.blue, '#FFFFFF', '#FFE066']} />

// Feed row like bump
const bump = useSharedValue(1);
onLike: bump.value = withSequence(withSpring(1.2), withSpring(1));
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons):

| Purpose | Ionicons |
|---------|----------|
| Pay arrow | `arrow-up` |
| Request arrow | `arrow-down` |
| Like | `heart-outline` / `heart` |
| Comment | `chatbubble-outline` |
| Share | `share-outline` |
| Send (comment) | `arrow-up-circle` |
| Search | `search` |
| QR scan | `qr-code-outline` |
| Home (tab) | `home` |
| Cards (tab) | `card` |
| Me (tab) | `person` |
| Clock (Incomplete) | `time` |
| Verified badge | `checkmark-circle` |

## 7. Platform Notes

- **Safe area**: Wrap the root in `SafeAreaView` from `react-native-safe-area-context`. Add 24pt of bottom padding above the home indicator for the Pay pill.
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` in light mode. Flip to `"light"` on the payment confirmation screen.
- **Dynamic Type**: Feed body, memo, comment scale freely. Balance and amount-entry Display fonts are layout-sensitive — cap at `maxFontSizeMultiplier={1.1}`. Tab labels fixed.
- **Dark mode**: Detect with `useColorScheme()`. Swap canvas to `#1A1A1A`, surfaces to `#242424`/`#2E2E2E`; brand blue stays.
- **Emoji rendering**: Rely on the OS emoji font — do NOT re-render emojis with custom art. Emoji memo culture depends on native rendering.
- **Tabular numerals**: Set `fontVariant: ['tabular-nums']` on every amount, balance, and digit display so columns align in activity lists.
- **Accessibility**:
  - Feed row: combine into one `accessibilityLabel` — "Alex paid Jordan, 2 hours ago. Memo: pizza and beer pizza Friday. 3 likes, 1 comment."
  - Pay pill: two children with `accessibilityRole="button"` and labels "Pay" / "Request"
  - Balance: label as "Venmo balance, $124.50"
  - Payment sent: use `AccessibilityInfo.announceForAccessibility("Payment sent")` after the animation
- **Haptics**:
  - `Haptics.impactAsync(Light)` on feed row like
  - `Haptics.impactAsync(Medium)` on Pay / Request pill tap
  - `Haptics.notificationAsync(Success)` on payment confirmation
  - `Haptics.selectionAsync()` on emoji chip tap
- **SVG for checkmark**: Use `react-native-svg` for the stroke-on path animation. Reanimated 3 supports animating `strokeDashoffset` via `useAnimatedProps`.
