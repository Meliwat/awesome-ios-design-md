# GroupMe (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates GroupMe's visual language into paste-ready Expo / React Native code: a design-token module, the colored chat nav bar, message bubbles, the signature heart-like pill, the image-gallery block, the composer, and the bottom tab bar.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand (interactive)
  gmBlue:        '#00AFF0',
  gmBluePressed: '#0091C7',
  gmBlueDeep:    '#0077B5',

  // Surfaces (light)
  canvas:        '#FFFFFF',
  surfaceGray:   '#F7F7F8',
  inboundLight:  '#F0F0F0',
  dividerLight:  '#E4E4E6',

  // Surfaces (dark)
  darkCanvas:    '#121212',
  darkSurface1:  '#1C1C1E',
  inboundDark:   '#2A2A2C',
  darkDivider:   '#2C2C2E',

  // Text
  textPrimary:       '#1A1A1A',
  textSecondary:     '#6A6A6A',
  textTertiary:      '#9A9A9A',
  darkTextPrimary:   '#ECECEC',
  darkTextSecondary: '#A0A0A0',
  onBlue:            '#FFFFFF',

  // Semantic
  likeHeart: '#FF3B5C',
  error:     '#F15E6C',
  success:   '#2ECC71',
  warning:   '#F1C40F',

  // Per-group theme accents
  themeCoral:  '#FF6B6B',
  themeGreen:  '#2ECC71',
  themePurple: '#9B59B6',
  themeTeal:   '#1ABC9C',
} as const;

export type GMColor = keyof typeof colors;

// Avatar generated gradients (assigned per member id)
export const avatarGradients: [string, string][] = [
  ['#FF6B6B', '#FF8E53'], // warm
  ['#2ECC71', '#16A085'], // green
  ['#9B59B6', '#6C5CE7'], // purple
  ['#4A90D9', '#1E3A5F'], // blue
  ['#F39C12', '#B5651D'], // amber
];
export const gradientForId = (id: number) =>
  avatarGradients[Math.abs(id) % avatarGradients.length];

// Per-group theme accents (recolor avatars + sender names; blue chrome stays constant)
export const groupThemes = {
  default: { accent: '#00AFF0', gradient: ['#00AFF0', '#0077B5'] },
  coral:   { accent: '#FF6B6B', gradient: ['#FF6B6B', '#FF8E53'] },
  green:   { accent: '#2ECC71', gradient: ['#2ECC71', '#16A085'] },
  purple:  { accent: '#9B59B6', gradient: ['#9B59B6', '#6C5CE7'] },
  sunset:  { accent: '#FF8E53', gradient: ['#FF6B6B', '#9B59B6'] },
  teal:    { accent: '#1ABC9C', gradient: ['#1ABC9C', '#16A085'] },
} as const;
```

## 2. Typography

GroupMe ships no brand typeface — use the system face. On iOS that's SF Pro automatically (no `fontFamily` needed). Dynamic Type scales `<Text>` by default.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#1A1A1A' } satisfies TextStyle;

export const typography = {
  largeNav:   { ...primary, fontSize: 32, fontWeight: '800', lineHeight: 37, letterSpacing: -0.4 },
  screen:     { ...primary, fontSize: 26, fontWeight: '700', lineHeight: 31, letterSpacing: -0.3 },
  groupName:  { color: '#FFFFFF', fontSize: 22, fontWeight: '700', lineHeight: 26, letterSpacing: -0.2 },
  section:    { ...primary, fontSize: 18, fontWeight: '700', lineHeight: 23 },
  body:       { ...primary, fontSize: 16, fontWeight: '400', lineHeight: 22 },
  rowTitle:   { ...primary, fontSize: 15, fontWeight: '600', lineHeight: 20 },
  preview:    { color: '#6A6A6A', fontSize: 14, fontWeight: '400', lineHeight: 18 },
  meta:       { color: '#6A6A6A', fontSize: 14, fontWeight: '400', lineHeight: 18 },
  senderName: { fontSize: 12, fontWeight: '600', lineHeight: 14, letterSpacing: 0.1 },
  likeCount:  { color: '#6A6A6A', fontSize: 10, fontWeight: '700', lineHeight: 10 },
  tab:        { color: '#9A9A9A', fontSize: 10, fontWeight: '500', lineHeight: 10 },
  unread:     { color: '#FFFFFF', fontSize: 12, fontWeight: '700', lineHeight: 12 },
  button:     { color: '#FFFFFF', fontSize: 16, fontWeight: '700', lineHeight: 16 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Chat Nav Bar (the colored header)

```tsx
// components/ChatNavBar.tsx
import { View, Text, Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChatNavBar({
  groupName, subline, gradient, initials, onBack, onInfo,
}: {
  groupName: string; subline: string; gradient: [string, string];
  initials: string; onBack: () => void; onInfo: () => void;
}) {
  return (
    <View style={{
      height: 56, flexDirection: 'row', alignItems: 'center',
      gap: 10, paddingHorizontal: 14, backgroundColor: colors.gmBlue,
    }}>
      <Pressable onPress={onBack} hitSlop={8}>
        <Ionicons name="chevron-back" size={22} color={colors.onBlue} />
      </Pressable>
      <LinearGradient colors={gradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ width: 34, height: 34, borderRadius: 17, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ color: '#FFF', fontSize: 14, fontWeight: '700' }}>{initials}</Text>
      </LinearGradient>
      <View style={{ flex: 1 }}>
        <Text style={typography.groupName} numberOfLines={1}>{groupName}</Text>
        <Text style={{ color: 'rgba(255,255,255,0.82)', fontSize: 11 }} numberOfLines={1}>{subline}</Text>
      </View>
      <Pressable onPress={onInfo} hitSlop={8}>
        <Ionicons name="information-circle-outline" size={22} color={colors.onBlue} />
      </Pressable>
    </View>
  );
}
```

### Message Bubble + Like Pill

```tsx
// components/MessageBubble.tsx
import { useEffect } from 'react';
import { Text, View, useColorScheme } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MessageBubble({
  text, outbound, likeCount = 0,
}: { text: string; outbound: boolean; likeCount?: number }) {
  const dark = useColorScheme() === 'dark';
  const bg = outbound ? colors.gmBlue : dark ? colors.inboundDark : colors.inboundLight;
  const fg = outbound ? '#FFFFFF' : dark ? colors.darkTextPrimary : colors.textPrimary;

  return (
    <View style={{ maxWidth: 260, alignSelf: outbound ? 'flex-end' : 'flex-start' }}>
      <View style={{
        backgroundColor: bg,
        paddingVertical: 9, paddingHorizontal: 13,
        borderRadius: 18,
        borderBottomLeftRadius: outbound ? 18 : 5,
        borderBottomRightRadius: outbound ? 5 : 18,
      }}>
        <Text style={[typography.body, { color: fg }]}>{text}</Text>
      </View>
      {likeCount > 0 && <LikePill count={likeCount} outbound={outbound} dark={dark} />}
    </View>
  );
}

function LikePill({ count, outbound, dark }: { count: number; outbound: boolean; dark: boolean }) {
  const scale = useSharedValue(0.6);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  useEffect(() => { scale.value = withSpring(1, { damping: 12, stiffness: 220 }); }, []);

  return (
    <Animated.View style={[{
      position: 'absolute', bottom: -10,
      [outbound ? 'right' : 'left']: 8,
      flexDirection: 'row', alignItems: 'center', gap: 3,
      paddingVertical: 2, paddingLeft: 5, paddingRight: 7,
      borderRadius: 999,
      backgroundColor: dark ? colors.darkSurface1 : colors.canvas,
      borderWidth: 1, borderColor: dark ? colors.darkDivider : colors.dividerLight,
    }, style]}>
      <Ionicons name="heart" size={11} color={colors.likeHeart} />
      <Text style={typography.likeCount}>{count}</Text>
    </Animated.View>
  );
}
```

### Inbound Sender Row (avatar + colored name above a run)

```tsx
// components/InboundRow.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { MessageBubble } from './MessageBubble';
import { typography } from '../theme/typography';

export function InboundRow({
  senderName, gradient, initials, accent, bubbles,
}: {
  senderName: string; gradient: [string, string]; initials: string;
  accent: string; bubbles: string[];
}) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 8 }}>
      <LinearGradient colors={gradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ width: 30, height: 30, borderRadius: 15, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ color: '#FFF', fontSize: 12, fontWeight: '700' }}>{initials}</Text>
      </LinearGradient>
      <View style={{ gap: 3, flexShrink: 1 }}>
        <Text style={[typography.senderName, { color: accent, marginLeft: 4 }]}>{senderName}</Text>
        {bubbles.map((b, i) => <MessageBubble key={i} text={b} outbound={false} />)}
      </View>
    </View>
  );
}
```

### Image Gallery Block

```tsx
// components/GalleryBlock.tsx
import { View, Text, Image, Pressable } from 'react-native';

export function GalleryBlock({ uris, onOpen }: { uris: string[]; onOpen: () => void }) {
  const shown = uris.slice(0, 4);
  const overflow = uris.length - 4;
  return (
    <Pressable onPress={onOpen} style={{
      padding: 4, borderRadius: 18, borderBottomLeftRadius: 5,
      backgroundColor: '#F0F0F0', alignSelf: 'flex-start',
    }}>
      <View style={{ flexDirection: 'row', flexWrap: 'wrap', width: 187, gap: 3 }}>
        {shown.map((u, i) => (
          <View key={i} style={{ width: 92, height: 92, borderRadius: 8, overflow: 'hidden' }}>
            <Image source={{ uri: u }} style={{ width: '100%', height: '100%' }} />
            {i === 3 && overflow > 0 && (
              <View style={{
                position: 'absolute', inset: 0, backgroundColor: 'rgba(0,0,0,0.5)',
                alignItems: 'center', justifyContent: 'center',
              }}>
                <Text style={{ color: '#FFF', fontSize: 16, fontWeight: '700' }}>+{overflow}</Text>
              </View>
            )}
          </View>
        ))}
      </View>
    </Pressable>
  );
}
```

### Composer

```tsx
// components/Composer.tsx
import { useState } from 'react';
import { View, TextInput, Pressable, useColorScheme } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function Composer({ onSend, onAttach }: { onSend: (t: string) => void; onAttach: () => void }) {
  const [text, setText] = useState('');
  const dark = useColorScheme() === 'dark';
  const send = () => {
    if (!text.trim()) return;
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    onSend(text); setText('');
  };
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 10,
      paddingHorizontal: 14, paddingTop: 10, paddingBottom: 8,
      backgroundColor: dark ? colors.darkSurface1 : colors.canvas,
      borderTopWidth: 0.5, borderTopColor: dark ? colors.darkDivider : colors.dividerLight,
    }}>
      <Pressable onPress={onAttach} hitSlop={8}>
        <Ionicons name="add-circle-outline" size={26} color={colors.gmBlue} />
      </Pressable>
      <TextInput
        value={text} onChangeText={setText} placeholder="Send a message"
        placeholderTextColor={colors.textTertiary} multiline
        style={[typography.body, {
          flex: 1, minHeight: 38, paddingHorizontal: 16,
          borderRadius: 999,
          backgroundColor: dark ? colors.inboundDark : colors.inboundLight,
        }]}
      />
      <Pressable onPress={send} disabled={!text.trim()} style={{
        width: 38, height: 38, borderRadius: 19, alignItems: 'center', justifyContent: 'center',
        backgroundColor: text.trim() ? colors.gmBlue : '#C8C8CC',
      }}>
        <Ionicons name="paper-plane" size={16} color="#FFF" />
      </Pressable>
    </View>
  );
}
```

### Chats List Row

```tsx
// components/ChatRow.tsx
import { View, Text, Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChatRow({
  groupName, preview, time, unread, gradient, initials, onPress,
}: {
  groupName: string; preview: string; time: string; unread: number;
  gradient: [string, string]; initials: string; onPress: () => void;
}) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      height: 72, flexDirection: 'row', alignItems: 'center', gap: 12,
      paddingHorizontal: 16, backgroundColor: pressed ? '#ECECEE' : 'transparent',
    })}>
      <LinearGradient colors={gradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ width: 52, height: 52, borderRadius: 26, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ color: '#FFF', fontSize: 18, fontWeight: '700' }}>{initials}</Text>
      </LinearGradient>
      <View style={{ flex: 1, gap: 3 }}>
        <Text style={typography.rowTitle}>{groupName}</Text>
        <Text style={typography.preview} numberOfLines={1}>{preview}</Text>
      </View>
      <View style={{ alignItems: 'flex-end', gap: 6 }}>
        <Text style={{ color: colors.textTertiary, fontSize: 12 }}>{time}</Text>
        {unread > 0 && (
          <View style={{ paddingHorizontal: 7, paddingVertical: 3, borderRadius: 999, backgroundColor: colors.gmBlue }}>
            <Text style={typography.unread}>{unread}</Text>
          </View>
        )}
      </View>
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
        tabBarActiveTintColor:  colors.gmBlue,   // active = GroupMe Blue
        tabBarInactiveTintColor: '#888888',
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.dividerLight },
        tabBarLabelStyle: { fontSize: 10, fontWeight: '500' },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Chats',    tabBarIcon: ({ color }) => <Ionicons name="chatbubbles" size={22} color={color} /> }} />
      <Tabs.Screen name="people"   options={{ title: 'People',   tabBarIcon: ({ color }) => <Ionicons name="people" size={22} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="search" size={22} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Like pill pop
scale.value = withSpring(1, { damping: 12, stiffness: 220 });

// Outbound send — slide up + fade from composer
import Animated, { FadeInDown } from 'react-native-reanimated';
// <Animated.View entering={FadeInDown.duration(200)}>

// New inbound — fade + 6pt slide-up
import { FadeIn } from 'react-native-reanimated';
// entering={FadeIn.duration(180)}

// Theme change — cross-fade avatar gradients & sender colors
import { withTiming } from 'react-native-reanimated';
// accent.value = withTiming(next, { duration: 250 });

// Attachment sheet — @gorhom/bottom-sheet, 300ms

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // on like
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);  // on send
Haptics.selectionAsync();                                 // on tab change
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Chats (tab) | `chatbubbles` |
| People (tab) | `people` |
| Discover (tab) | `search` |
| Profile (tab) | `person-circle` |
| Back | `chevron-back` |
| Group info | `information-circle-outline` |
| Like | `heart` / `heart-outline` |
| Attach | `add-circle-outline` |
| Send | `paper-plane` |
| Camera | `camera` |
| Photo library | `images` |
| GIF | `happy` |
| Poll | `bar-chart` |
| Event / Calendar | `calendar` |
| Location | `location` |
| Mute | `notifications-off` |
| Search | `search` |
| Gallery (per group) | `grid` |

## 7. Platform Notes

- **Font choice**: GroupMe ships no brand typeface — do not set `fontFamily`; iOS renders SF Pro automatically. Dynamic Type scales `<Text>` by default.
- **Colored header**: keep the chat nav bar solid `#00AFF0` on both color schemes — it is the signature chrome; pair `<StatusBar style="light" />` so status text stays readable over the blue bar.
- **Status bar**: `light` while a thread is open (blue nav bar); `dark` on light list screens, `light` on dark mode list screens.
- **Safe area**: wrap screens in `SafeAreaView`; the blue nav bar should extend into the top inset (`edges={['top']}` excluded for the nav background) while content stays within the safe area; composer + tab bar respect the home indicator.
- **Like overlap**: render the like pill with `position: 'absolute'` and `bottom: -10` so it hangs attached to the bubble — keep enough container padding that it isn't clipped.
- **Bubble run grouping**: collapse consecutive same-sender messages within 60s — render avatar + colored name once at the top, then stacked bubbles with 3pt gap.
- **Per-group theme**: thread accent (sender names + avatar gradients) comes from `groupThemes[groupTheme]`; the blue chrome (nav bar, outbound bubbles, send button, active tab) never changes.
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `inboundDark` / `darkSurface1`; never pure black — `#121212` keeps GroupMe warm.
- **Accessibility**: expose double-tap-to-like via `accessibilityActions={[{ name: 'like', label: 'Like' }]}`; label the like pill with the count; keep `allowFontScaling={false}` on tab labels, like-count numbers, and unread pills.
- **Image gallery**: use `expo-image` for cached thumbnails; the `+N` overflow scrim is `rgba(0,0,0,0.5)` with white 16pt bold.
