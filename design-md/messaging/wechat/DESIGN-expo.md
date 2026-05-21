# WeChat (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates WeChat's visual language into paste-ready Expo / React Native code: a design-token module, the squarer tailed bubbles, rounded-square avatars, and the signature Discover grouped-list hub.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  backdrop:   '#EDEDED',  // chat + grouped-list base
  canvas:     '#FFFFFF',
  surface:    '#F7F7F7',
  divider:    '#D9D9D9',
  pressedRow: '#ECECEC',

  textPrimary:   '#181818',
  textSecondary: '#888888',
  textTertiary:  '#B2B2B2',

  green:        '#07C160',
  greenPressed: '#06A050',

  incoming: '#FFFFFF',
  outgoing: '#95EC69',   // pale chat-green — NOT the brand green

  redDot:   '#FA5151',
  linkBlue: '#576B95',
  gold:     '#FBE3B3',

  // Discover icon chips (data-driven)
  moments:  '#3F7DD5',
  scan:     '#2EA0F8',
  channels: '#FA9D3B',
  mini:     '#3CC51F',
  search:   '#5C7CFA',
  nearby:   '#F76F34',
  topStories: '#EB6F6F',
} as const;

export type WeChatColor = keyof typeof colors;
```

## 2. Typography

WeChat uses **PingFang SC** (iOS system CJK face) / Noto Sans at weights 400/600. On iOS, `'PingFang SC'` resolves natively; load Noto Sans for Android Latin parity.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'NotoSans-Regular':  require('../assets/fonts/NotoSans-Regular.ttf'),
    'NotoSans-SemiBold': require('../assets/fonts/NotoSans-SemiBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import { Platform } from 'react-native';
import type { TextStyle } from 'react-native';

const sc  = Platform.select({ ios: 'PingFang SC', default: 'NotoSans-Regular' });
const scB = Platform.select({ ios: 'PingFang SC', default: 'NotoSans-SemiBold' });

export const typography = {
  navTitle:    { fontFamily: scB, fontWeight: '600' as const, fontSize: 17, lineHeight: 22 },
  contactName: { fontFamily: sc,  fontSize: 17, lineHeight: 22 },
  rowTitle:    { fontFamily: sc,  fontSize: 17, lineHeight: 22 },
  messageBody: { fontFamily: sc,  fontSize: 17, lineHeight: 24 }, // CJK-friendly 1.4
  section:     { fontFamily: sc,  fontSize: 14, lineHeight: 17 },
  preview:     { fontFamily: sc,  fontSize: 14, lineHeight: 18 },
  timestamp:   { fontFamily: sc,  fontSize: 13, lineHeight: 16 },
  timeSep:     { fontFamily: sc,  fontSize: 12, lineHeight: 14 },
  button:      { fontFamily: scB, fontWeight: '600' as const, fontSize: 17, lineHeight: 17 },
  buttonSec:   { fontFamily: sc,  fontSize: 16, lineHeight: 16 },
  tab:         { fontFamily: sc,  fontSize: 10, lineHeight: 12 },
  badge:       { fontFamily: scB, fontWeight: '600' as const, fontSize: 11, lineHeight: 11 },
  momentsName: { fontFamily: scB, fontWeight: '600' as const, fontSize: 15, lineHeight: 20 },
  caption:     { fontFamily: sc,  fontSize: 13, lineHeight: 17 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Discover Grouped-List Hub (Signature — the super-app)

```tsx
// components/Discover.tsx
import { ScrollView, Text, View, Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

function Row({ icon, color, title, redDot }: { icon: any; color: string; title: string; redDot?: boolean }) {
  return (
    <Pressable style={({ pressed }) => [styles.row, pressed && { backgroundColor: colors.pressedRow }]}>
      <View style={[styles.chip, { backgroundColor: color }]}>
        <Ionicons name={icon} size={15} color="#FFF" />
      </View>
      <Text style={[typography.rowTitle, { color: colors.textPrimary }]}>{title}</Text>
      <View style={{ flex: 1 }} />
      {redDot && <View style={styles.dot} />}
      <Ionicons name="chevron-forward" size={16} color={colors.textTertiary} />
    </Pressable>
  );
}

const Divider = () => <View style={styles.divider} />;

export function Discover() {
  return (
    <ScrollView style={{ backgroundColor: colors.backdrop }} contentContainerStyle={{ paddingVertical: 12 }}>
      <View style={styles.cluster}>
        <Row icon="images-outline" color={colors.moments} title="Moments" redDot />
      </View>
      <View style={styles.cluster}>
        <Row icon="play-circle-outline" color={colors.channels} title="Channels" />
        <Divider />
        <Row icon="scan-outline" color={colors.scan} title="Scan" />
        <Divider />
        <Row icon="document-text-outline" color={colors.topStories} title="Top Stories" />
      </View>
      <View style={styles.cluster}>
        <Row icon="search-outline" color={colors.search} title="Search" />
        <Divider />
        <Row icon="location-outline" color={colors.nearby} title="People Nearby" />
      </View>
      <View style={styles.cluster}>
        <Row icon="grid-outline" color={colors.mini} title="Mini Programs" />
      </View>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  cluster: { backgroundColor: colors.canvas, marginBottom: 12 }, // gray gutter between clusters
  row:     { height: 56, flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, gap: 12,
             backgroundColor: colors.canvas },
  chip:    { width: 28, height: 28, borderRadius: 8, alignItems: 'center', justifyContent: 'center' },
  divider: { height: StyleSheet.hairlineWidth, backgroundColor: colors.divider, marginLeft: 56 },
  dot:     { width: 8, height: 8, borderRadius: 4, backgroundColor: colors.redDot, marginRight: 8 },
});
```

### Tailed Message Bubble + Rounded-Square Avatar (Signature)

```tsx
// components/WCBubble.tsx
import { Text, View, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

// Rounded-SQUARE avatar — WeChat's constant brand tell (never a circle)
const Avatar = () => <View style={styles.avatar} />;

export function WCBubble({ text, isOutgoing }: { text: string; isOutgoing: boolean }) {
  return (
    <View style={[styles.row, { justifyContent: isOutgoing ? 'flex-end' : 'flex-start' }]}>
      {!isOutgoing && <Avatar />}
      <View style={styles.bubbleWrap}>
        {/* triangular tail toward the sender's avatar */}
        {!isOutgoing && <View style={[styles.tail, styles.tailIn]} />}
        <View style={[styles.bubble, { backgroundColor: isOutgoing ? colors.outgoing : colors.incoming }]}>
          <Text style={[typography.messageBody, { color: colors.textPrimary }]}>{text}</Text>
        </View>
        {isOutgoing && <View style={[styles.tail, styles.tailOut]} />}
      </View>
      {isOutgoing && <Avatar />}
    </View>
  );
}

const styles = StyleSheet.create({
  row:        { flexDirection: 'row', alignItems: 'flex-start', gap: 8,
                paddingHorizontal: 8, paddingVertical: 2 },
  avatar:     { width: 40, height: 40, borderRadius: 8, backgroundColor: colors.surface },
  bubbleWrap: { flexDirection: 'row', alignItems: 'flex-start', maxWidth: '72%' },
  bubble:     { paddingVertical: 9, paddingHorizontal: 12, borderRadius: 6 },
  tail:       { width: 0, height: 0, marginTop: 12,
                borderTopWidth: 5, borderBottomWidth: 5, borderTopColor: 'transparent', borderBottomColor: 'transparent' },
  tailIn:     { borderRightWidth: 6, borderRightColor: colors.incoming },
  tailOut:    { borderLeftWidth: 6, borderLeftColor: colors.outgoing },
});
```

### Chat List Row (rounded-square avatar + red badge)

```tsx
// components/ChatRow.tsx
import { Pressable, Text, View, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChatRow({
  name, preview, time, unread,
}: { name: string; preview: string; time: string; unread: number }) {
  return (
    <Pressable style={({ pressed }) => [styles.row, pressed && { backgroundColor: colors.pressedRow }]}>
      <View style={styles.avatar} />
      <View style={{ flex: 1, gap: 4 }}>
        <Text style={[typography.contactName, { color: colors.textPrimary }]}>{name}</Text>
        <Text style={[typography.preview, { color: colors.textSecondary }]} numberOfLines={1}>{preview}</Text>
      </View>
      <View style={{ alignItems: 'flex-end', gap: 6 }}>
        <Text style={[typography.timestamp, { color: colors.textSecondary }]}>{time}</Text>
        {unread > 0 && (
          <View style={styles.badge}><Text style={[typography.badge, { color: '#FFF' }]}>{unread}</Text></View>
        )}
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row:    { height: 64, flexDirection: 'row', alignItems: 'center', gap: 12,
            paddingHorizontal: 12, backgroundColor: colors.canvas },
  avatar: { width: 48, height: 48, borderRadius: 8, backgroundColor: colors.surface }, // rounded square
  badge:  { minWidth: 18, height: 18, borderRadius: 9, paddingHorizontal: 5,
            alignItems: 'center', justifyContent: 'center', backgroundColor: colors.redDot },
});
```

### Red Packet (Hóngbāo) Card (Signature)

```tsx
// components/RedPacket.tsx
import { useState } from 'react';
import { Pressable, Text, View, StyleSheet } from 'react-native';
import Animated, { useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RedPacket({ sender }: { sender: string }) {
  const [opened, setOpened] = useState(false);
  const seal = useAnimatedStyle(() => ({
    transform: [{ rotateY: withTiming(opened ? '360deg' : '0deg', { duration: 600 }) }],
  }));
  return (
    <Pressable onPress={() => setOpened(true)}>
      <LinearGradient colors={[colors.redDot, '#C72E29']} style={styles.card}>
        <Animated.View style={[styles.seal, seal]}>
          <Ionicons name={opened ? 'mail-open' : 'logo-yen'} size={22} color="#C72E29" />
        </Animated.View>
        <Text style={[typography.button, { color: '#FFF' }]}>Best wishes!</Text>
        <Text style={[typography.caption, { color: 'rgba(255,255,255,0.85)' }]}>From {sender}</Text>
      </LinearGradient>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  card: { width: 260, paddingVertical: 22, borderRadius: 8, alignItems: 'center', gap: 10,
          shadowColor: '#000', shadowOpacity: 0.12, shadowRadius: 4, shadowOffset: { width: 0, height: 2 }, elevation: 2 },
  seal: { width: 44, height: 44, borderRadius: 22, backgroundColor: colors.gold,
          alignItems: 'center', justifyContent: 'center' },
});
```

### Composer

```tsx
// components/ComposerBar.tsx
import { useState } from 'react';
import { Pressable, TextInput, Text, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ComposerBar() {
  const [text, setText] = useState('');
  const hasText = text.trim().length > 0;
  return (
    <View style={styles.bar}>
      <Ionicons name="mic-outline" size={26} color={colors.textPrimary} />
      <TextInput
        style={[typography.messageBody, styles.field]}
        value={text} onChangeText={setText} multiline
      />
      <Ionicons name="happy-outline" size={26} color={colors.textPrimary} />
      {hasText ? (
        <Pressable onPress={() => setText('')} style={styles.send}>
          <Text style={[typography.buttonSec, { color: '#FFF' }]}>Send</Text>
        </Pressable>
      ) : (
        <Ionicons name="add-circle-outline" size={26} color={colors.textPrimary} />
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  bar:   { flexDirection: 'row', alignItems: 'flex-end', gap: 10,
           paddingHorizontal: 8, paddingVertical: 8, backgroundColor: colors.backdrop },
  field: { flex: 1, minHeight: 36, maxHeight: 110, paddingHorizontal: 10,
           borderRadius: 6, backgroundColor: colors.canvas, color: colors.textPrimary },
  send:  { paddingVertical: 8, paddingHorizontal: 14, borderRadius: 4, backgroundColor: colors.green },
});
```

## 4. Time Separator

```tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export const TimeSeparator = ({ label }: { label: string }) => (
  <View style={{ alignItems: 'center', paddingVertical: 8 }}>
    <Text style={[typography.timeSep, { color: colors.textSecondary }]}>{label}</Text>
  </View>
);
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.green,
        tabBarInactiveTintColor: '#999999',
        tabBarStyle: { backgroundColor: colors.surface, borderTopColor: colors.divider, borderTopWidth: 1 },
        tabBarLabelStyle: { fontFamily: 'NotoSans-Regular', fontSize: 10 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Chats',    tabBarIcon: ({ color }) => <Ionicons name="chatbubble" size={26} color={color} /> }} />
      <Tabs.Screen name="contacts" options={{ title: 'Contacts', tabBarIcon: ({ color }) => <Ionicons name="people" size={26} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="compass" size={26} color={color} /> }} />
      <Tabs.Screen name="me"       options={{ title: 'Me',       tabBarIcon: ({ color }) => <Ionicons name="person" size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Red-packet open (the one flourish) — see RedPacket (rotateY 600ms)

// Send (text) — quick fade + slight scale
import Animated, { FadeIn } from 'react-native-reanimated';
<Animated.View entering={FadeIn.duration(150)} />

// Attachment grid present — slide up; "+" rotates to "×"
import { SlideInDown } from 'react-native-reanimated';
<Animated.View entering={SlideInDown.duration(260)} />

// Press states — Pressable style callback toggling backgroundColor (~120ms via LayoutAnimation if needed)

// Red dot — render/unrender with NO entering/exiting animation (authoritative, not playful)
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to WeChat's glyphs:

| Purpose | Ionicons |
|---------|----------|
| Voice | `mic-outline` |
| Sticker | `happy-outline` |
| Plus / attachment | `add-circle-outline` |
| Moments (Discover) | `images-outline` |
| Scan (Discover) | `scan-outline` |
| Channels (Discover) | `play-circle-outline` |
| Mini Programs (Discover) | `grid-outline` |
| Search (Discover) | `search-outline` |
| Nearby (Discover) | `location-outline` |
| Chevron (rows) | `chevron-forward` |
| Red packet | `logo-yen` / `mail-open` |
| Chats (tab) | `chatbubble` |
| Contacts (tab) | `people` |
| Discover (tab) | `compass` |
| Me (tab) | `person` |

## 8. Platform Notes

- **Discover is a grouped list**, not a dashboard — white rows, 12pt gray gutters between clusters, hairline dividers within; this screen is WeChat's identity
- **Rounded-square avatars**: every avatar is `borderRadius: 8` on a square — never a circle (constant brand tell)
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` — the gray/white system
- **CJK**: use `'PingFang SC'` on iOS (resolves natively); keep `lineHeight` generous (≈1.4 on body) so dense CJK isn't cramped
- **Safe area**: wrap screens in `SafeAreaView`; composer + attachment grid rise with the keyboard via `KeyboardAvoidingView`
- **Dynamic Type**: RN respects font scaling; set `allowFontScaling={false}` on tab labels, badge counts, time separators
- **Accessibility**: announce the Discover row's red dot as "has new updates"; label the red-packet seal "Red packet from <sender>, double-tap to open"
- **Reduce Motion**: gate the red-packet `rotateY` behind `AccessibilityInfo.isReduceMotionEnabled()` and crossfade to the opened state; the red dot must never animate
