# Telegram (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Telegram's visual language into paste-ready Expo / React Native code: a design-token module, themed components, Reanimated + Haptics snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, `@expo/vector-icons`, `lottie-react-native`, and `react-native-reanimated` v3.

## 1. Color Tokens

Telegram is deeply themeable — expose `accent` as a context value so components re-tint when the user picks a different color.

```ts
// theme/colors.ts
export const colors = {
  // Default accent (themeable)
  accent:        '#0088CC',
  accentLight:   '#40A7E3',
  accentPressed: '#0071B0',

  // Bubble (default blue)
  bubbleOutgoing:      '#2B86FD',
  bubbleOutgoingTop:   '#2B86FD',
  bubbleOutgoingBot:   '#61B3FF',
  bubbleIncomingLight: '#FFFFFF',
  bubbleIncomingDark:  '#2A2A2A',

  // Canvas
  canvasLight:   '#FFFFFF',
  canvasDark:    '#212121',
  canvasOLED:    '#000000',
  chatBGBlue:    '#DBE7F4',
  surface1Light: '#F7F7F7',
  surface1Dark:  '#1C1C1C',
  surface2Dark:  '#2C2C2E',
  dividerLight:  '#C7C7CC',
  dividerDark:   '#383838',

  // Text
  textPrimary:       '#000000',
  textSecondary:     '#707579',
  textTertiary:      '#A0A6AD',
  textPrimaryDark:   '#FFFFFF',
  textSecondaryDark: '#8D8E93',

  // Semantic
  onlineGreen: '#4DD364',
  errorRed:    '#E53935',
  destructive: '#E35561',
  premiumA:    '#AE6FFD',
  premiumB:    '#CE6BFF',
} as const;

export const senderColors = [
  '#FC5C51', '#FA790F', '#895DD5', '#0FB297',
  '#00C1A6', '#3CA5EC', '#FF5274',
] as const;

export function senderColorFor(id: number) {
  return senderColors[Math.abs(id) % senderColors.length];
}

export type TgColor = keyof typeof colors;
```

### Theme Context

```tsx
// theme/ThemeProvider.tsx
import { createContext, useContext, useState } from 'react';

export type TelegramTheme = {
  accent: string;
  outgoingBubble: string;
  useGradientBubbles: boolean;
  isDark: boolean;
  isOLED: boolean;
};

const defaultTheme: TelegramTheme = {
  accent: '#0088CC',
  outgoingBubble: '#2B86FD',
  useGradientBubbles: false,
  isDark: false,
  isOLED: false,
};

const ThemeContext = createContext<{
  theme: TelegramTheme;
  setTheme: (t: Partial<TelegramTheme>) => void;
}>({ theme: defaultTheme, setTheme: () => {} });

export function TelegramThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setState] = useState(defaultTheme);
  return (
    <ThemeContext.Provider value={{ theme, setTheme: (t) => setState((p) => ({ ...p, ...t })) }}>
      {children}
    </ThemeContext.Provider>
  );
}

export const useTelegramTheme = () => useContext(ThemeContext);
```

## 2. Typography

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const sys = undefined; // iOS default = SF Pro

export const typography = {
  largeTitle:      { fontFamily: sys, fontSize: 34, fontWeight: '700' as const, letterSpacing: 0.37 },
  navTitle:        { fontFamily: sys, fontSize: 17, fontWeight: '600' as const },
  contactName:     { fontFamily: sys, fontSize: 17, fontWeight: '600' as const },
  bubbleBody:      { fontFamily: sys, fontSize: 17, fontWeight: '400' as const },
  groupSender:     { fontFamily: sys, fontSize: 14, fontWeight: '600' as const },
  messagePreview:  { fontFamily: sys, fontSize: 15, fontWeight: '400' as const, color: '#707579' },
  timestampList:   { fontFamily: sys, fontSize: 13, fontWeight: '400' as const, color: '#707579' },
  timestampBubble: { fontFamily: sys, fontSize: 11, fontWeight: '400' as const },
  caption:         { fontFamily: sys, fontSize: 12, fontWeight: '400' as const },
  tabLabel:        { fontFamily: sys, fontSize: 10, fontWeight: '500' as const },
  button:          { fontFamily: sys, fontSize: 17, fontWeight: '600' as const, color: '#FFFFFF' },
  code:            { fontFamily: 'Menlo', fontSize: 15, fontWeight: '400' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Compose Bar (with morphing send + silent long-press)

```tsx
// components/ComposeBar.tsx
import { useState } from 'react';
import { View, TextInput, Pressable, Text, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { useTelegramTheme } from '../theme/ThemeProvider';
import { colors } from '../theme/colors';

export function ComposeBar({ onSend, onSilentSend, onSchedule }: {
  onSend: (text: string) => void;
  onSilentSend: (text: string) => void;
  onSchedule: (text: string) => void;
}) {
  const { theme } = useTelegramTheme();
  const [text, setText] = useState('');
  const [menuOpen, setMenuOpen] = useState(false);

  return (
    <View style={styles.wrap}>
      <Pressable hitSlop={8}>
        <Ionicons name="attach" size={22} color={colors.textSecondary} />
      </Pressable>

      <TextInput
        placeholder="Message"
        placeholderTextColor={colors.textTertiary}
        value={text}
        onChangeText={setText}
        multiline
        style={styles.input}
      />

      <Pressable hitSlop={8}>
        <Ionicons name="happy-outline" size={22} color={colors.textSecondary} />
      </Pressable>

      {text.length === 0 ? (
        <Pressable hitSlop={8}>
          <Ionicons name="mic" size={22} color={theme.accent} />
        </Pressable>
      ) : (
        <Pressable
          hitSlop={8}
          onPress={() => {
            Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
            onSend(text);
            setText('');
          }}
          onLongPress={() => {
            Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
            setMenuOpen(true);
          }}
        >
          <Ionicons name="send" size={22} color={theme.accent} />
        </Pressable>
      )}

      {menuOpen && (
        <SilentSendMenu
          onSilent={() => { onSilentSend(text); setText(''); setMenuOpen(false); }}
          onSchedule={() => { onSchedule(text); setText(''); setMenuOpen(false); }}
          onDismiss={() => setMenuOpen(false)}
        />
      )}
    </View>
  );
}

function SilentSendMenu({ onSilent, onSchedule, onDismiss }: {
  onSilent: () => void; onSchedule: () => void; onDismiss: () => void;
}) {
  return (
    <Pressable onPress={onDismiss} style={StyleSheet.absoluteFill}>
      <View style={styles.menuPopover}>
        <Pressable style={styles.menuRow} onPress={onSilent}>
          <Ionicons name="volume-mute-outline" size={18} color={colors.textPrimary} />
          <Text style={styles.menuText}>Send Without Sound</Text>
        </Pressable>
        <Pressable style={styles.menuRow} onPress={onSchedule}>
          <Ionicons name="calendar-outline" size={18} color={colors.textPrimary} />
          <Text style={styles.menuText}>Schedule Message</Text>
        </Pressable>
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  wrap: { flexDirection: 'row', alignItems: 'flex-end', gap: 16, paddingHorizontal: 16, paddingVertical: 10, backgroundColor: colors.surface1Light, borderTopWidth: StyleSheet.hairlineWidth, borderTopColor: colors.dividerLight },
  input: { flex: 1, fontSize: 16, color: colors.textPrimary, maxHeight: 120 },
  menuPopover: { position: 'absolute', right: 16, bottom: 56, backgroundColor: '#FFF', borderRadius: 14, paddingVertical: 8, shadowColor: '#000', shadowOpacity: 0.16, shadowRadius: 16, shadowOffset: { width: 0, height: 4 } },
  menuRow: { flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 14, paddingVertical: 10 },
  menuText: { fontSize: 15, color: colors.textPrimary },
});
```

### Outgoing Bubble (Solid + Gradient)

```tsx
// components/OutgoingBubble.tsx
import { View, Text, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useTelegramTheme } from '../theme/ThemeProvider';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function OutgoingBubble({ text, timestamp, isRead }: {
  text: string; timestamp: string; isRead: boolean;
}) {
  const { theme } = useTelegramTheme();

  const content = (
    <>
      <Text style={[typography.bubbleBody, { color: '#FFF' }]}>{text}</Text>
      <View style={styles.metaRow}>
        <Text style={[typography.timestampBubble, { color: 'rgba(255,255,255,0.8)' }]}>{timestamp}</Text>
        <Ionicons name={isRead ? 'checkmark-done' : 'checkmark'} size={14} color="rgba(255,255,255,0.9)" />
      </View>
    </>
  );

  return (
    <View style={styles.row}>
      {theme.useGradientBubbles ? (
        <LinearGradient
          colors={[colors.bubbleOutgoingTop, colors.bubbleOutgoingBot]}
          start={{ x: 0, y: 0 }} end={{ x: 0, y: 1 }}
          style={styles.bubble}
        >
          {content}
        </LinearGradient>
      ) : (
        <View style={[styles.bubble, { backgroundColor: theme.outgoingBubble }]}>
          {content}
        </View>
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  row: { width: '100%', alignItems: 'flex-end', paddingRight: 8, paddingVertical: 1 },
  bubble: {
    maxWidth: '80%',
    paddingVertical: 8,
    paddingHorizontal: 12,
    borderTopLeftRadius: 17,
    borderTopRightRadius: 17,
    borderBottomLeftRadius: 17,
    borderBottomRightRadius: 6, // the notch tail
    alignItems: 'flex-end',
  },
  metaRow: { flexDirection: 'row', alignItems: 'center', gap: 4, marginTop: 2 },
});
```

### Incoming Bubble (with Sender Color)

```tsx
// components/IncomingBubble.tsx
import { View, Text, StyleSheet } from 'react-native';
import { colors, senderColorFor } from '../theme/colors';
import { typography } from '../theme/typography';

export function IncomingBubble({ text, senderName, senderId }: {
  text: string; senderName?: string; senderId?: number;
}) {
  return (
    <View style={styles.row}>
      <View style={styles.bubble}>
        {senderName !== undefined && senderId !== undefined && (
          <Text style={[typography.groupSender, { color: senderColorFor(senderId) }]}>{senderName}</Text>
        )}
        <Text style={typography.bubbleBody}>{text}</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  row: { width: '100%', alignItems: 'flex-start', paddingLeft: 8, paddingVertical: 1 },
  bubble: {
    maxWidth: '80%',
    backgroundColor: '#FFFFFF',
    paddingVertical: 8,
    paddingHorizontal: 12,
    borderTopLeftRadius: 17,
    borderTopRightRadius: 17,
    borderBottomLeftRadius: 6, // notch
    borderBottomRightRadius: 17,
  },
});
```

### Floating Voice Mini-Player

```tsx
// components/VoiceMiniPlayer.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function VoiceMiniPlayer({ title, progress, onClose }: {
  title: string; progress: number; onClose: () => void;
}) {
  return (
    <View style={styles.pill}>
      <Ionicons name="pause" size={14} color={colors.accent} />
      <View style={styles.textCol}>
        <Text style={styles.title} numberOfLines={1}>{title}</Text>
        <View style={styles.progressTrack}>
          <View style={[styles.progressFill, { width: `${progress * 100}%` }]} />
        </View>
      </View>
      <Pressable onPress={onClose} hitSlop={10}>
        <Ionicons name="close" size={14} color={colors.textSecondary} />
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  pill: {
    flexDirection: 'row', alignItems: 'center', gap: 12,
    height: 40, paddingHorizontal: 14, borderRadius: 22,
    backgroundColor: '#FFFFFF', marginHorizontal: 16,
    shadowColor: '#000', shadowOpacity: 0.16, shadowRadius: 16, shadowOffset: { width: 0, height: 4 },
    elevation: 8,
  },
  textCol: { flex: 1, gap: 3 },
  title: { fontSize: 13, fontWeight: '600', color: colors.textPrimary },
  progressTrack: { height: 2, backgroundColor: colors.dividerLight, borderRadius: 1 },
  progressFill: { height: 2, backgroundColor: colors.accent, borderRadius: 1 },
});
```

### Swipe-to-Reply

```tsx
// components/SwipeToReply.tsx
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, runOnJS } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { View } from 'react-native';
import { colors } from '../theme/colors';

const THRESHOLD = 60;

export function SwipeToReply({ children, onReply }: { children: React.ReactNode; onReply: () => void }) {
  const tx = useSharedValue(0);
  const ticked = useSharedValue(false);

  const tick = () => Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

  const pan = Gesture.Pan()
    .onUpdate((e) => {
      tx.value = Math.max(0, Math.min(e.translationX, THRESHOLD + 20));
      if (tx.value >= THRESHOLD && !ticked.value) {
        ticked.value = true;
        runOnJS(tick)();
      }
    })
    .onEnd(() => {
      if (tx.value >= THRESHOLD) runOnJS(onReply)();
      tx.value = withSpring(0);
      ticked.value = false;
    });

  const style = useAnimatedStyle(() => ({ transform: [{ translateX: tx.value }] }));
  const iconStyle = useAnimatedStyle(() => ({ opacity: Math.min(1, tx.value / THRESHOLD) }));

  return (
    <GestureDetector gesture={pan}>
      <View>
        <Animated.View style={[{ position: 'absolute', left: -24, top: 0, bottom: 0, justifyContent: 'center' }, iconStyle]}>
          <Ionicons name="arrow-undo" size={20} color={colors.accent} />
        </Animated.View>
        <Animated.View style={style}>{children}</Animated.View>
      </View>
    </GestureDetector>
  );
}
```

### Animated Emoji (Lottie)

```tsx
// components/AnimatedEmoji.tsx
import LottieView from 'lottie-react-native';
import { View } from 'react-native';

export function AnimatedEmoji({ source, size = 72 }: { source: any; size?: number }) {
  return (
    <View style={{ width: size, height: size }}>
      <LottieView source={source} autoPlay loop={false} style={{ width: size, height: size }} />
    </View>
  );
}
```

## 4. Chat List Row

```tsx
// components/ChatListRow.tsx
import { View, Text, Image, Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChatListRow({
  avatarUri, name, preview, timestamp, unreadCount, isPinned, isMuted, onPress,
}: {
  avatarUri: string; name: string; preview: string; timestamp: string;
  unreadCount: number; isPinned: boolean; isMuted: boolean; onPress: () => void;
}) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => [
        styles.row,
        isPinned && { backgroundColor: colors.surface1Light },
        pressed && { backgroundColor: '#F2F2F7' },
      ]}
    >
      <Image source={{ uri: avatarUri }} style={styles.avatar} />
      <View style={styles.textCol}>
        <View style={styles.nameRow}>
          <Text style={typography.contactName} numberOfLines={1}>{name}</Text>
          {isMuted && <Ionicons name="volume-mute" size={13} color={colors.textSecondary} />}
        </View>
        <Text style={typography.messagePreview} numberOfLines={1}>{preview}</Text>
      </View>
      <View style={styles.metaCol}>
        <Text style={typography.timestampList}>{timestamp}</Text>
        <View style={styles.metaBottom}>
          {isPinned && <Ionicons name="pin" size={13} color={colors.textSecondary} />}
          {unreadCount > 0 && (
            <View style={[styles.unread, isMuted && { backgroundColor: colors.textTertiary }]}>
              <Text style={styles.unreadText}>{unreadCount}</Text>
            </View>
          )}
        </View>
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row: { height: 76, flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, gap: 12 },
  avatar: { width: 54, height: 54, borderRadius: 27, backgroundColor: colors.surface1Light },
  textCol: { flex: 1, gap: 4 },
  nameRow: { flexDirection: 'row', alignItems: 'center', gap: 4 },
  metaCol: { alignItems: 'flex-end', gap: 4 },
  metaBottom: { flexDirection: 'row', alignItems: 'center', gap: 4 },
  unread: { backgroundColor: colors.accent, minWidth: 22, height: 22, paddingHorizontal: 7, borderRadius: 11, alignItems: 'center', justifyContent: 'center' },
  unreadText: { color: '#FFF', fontSize: 13, fontWeight: '600' },
});
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { useTelegramTheme } from '../../theme/ThemeProvider';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  const { theme } = useTelegramTheme();
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: theme.accent,
        tabBarInactiveTintColor: colors.textSecondaryDark,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint={theme.isDark ? 'dark' : 'light'} style={{ flex: 1, backgroundColor: theme.isDark ? 'rgba(28,28,28,0.92)' : 'rgba(255,255,255,0.92)' }} />
        ),
        tabBarLabelStyle: { fontSize: 10, fontWeight: '500' },
      }}
    >
      <Tabs.Screen name="contacts" options={{ title: 'Contacts', tabBarIcon: ({ color }) => <Ionicons name="people" size={26} color={color} /> }} />
      <Tabs.Screen name="calls"    options={{ title: 'Calls',    tabBarIcon: ({ color }) => <Ionicons name="call" size={26} color={color} /> }} />
      <Tabs.Screen name="chats"    options={{ title: 'Chats',    tabBarIcon: ({ color }) => <Ionicons name="chatbubbles" size={26} color={color} />, tabBarBadge: 12 }} />
      <Tabs.Screen name="settings" options={{ title: 'Settings', tabBarIcon: ({ color }) => <Ionicons name="settings" size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Silent-send long-press
onLongPress={() => {
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
  setMenuOpen(true);
}}

// Swipe-to-reply threshold haptic (see SwipeToReply.tsx)

// Bubble arrival
const bubbleEnter = useSharedValue(0);
bubbleEnter.value = withSpring(1, { damping: 10, stiffness: 120 });

// Reactions palette entry (scale from bubble)
reactionScale.value = withSpring(1, { damping: 8, stiffness: 180 });

// Context menu — bubble lift
scale.value = withTiming(1.05, { duration: 180 });

// Voice mini-player slide in
translateY.value = withSpring(0);

// Interactive emoji (heart/rocket) — full-screen Lottie overlay
<LottieView source={require('./assets/heart-interactive.json')} autoPlay loop={false} onAnimationFinish={() => setOverlay(false)} />
```

## 7. Icon Library

Mix `@expo/vector-icons` Ionicons with custom Telegram brand SVGs for glyphs that don't map cleanly (broadcast channel, Premium star, sticker).

| Purpose | Ionicons | Notes |
|---------|----------|-------|
| Send | `send` | Standard |
| Mic | `mic` | |
| Attach | `attach` | |
| Emoji | `happy-outline` | |
| Sticker | custom SVG | Proprietary glyph |
| Read (single) | `checkmark` | |
| Read (double) | `checkmark-done` | |
| Phone | `call` | |
| Video | `videocam` | |
| Lock | `lock-closed` | Secret chat |
| Pin | `pin` | |
| Muted | `volume-mute` | |
| Chats | `chatbubbles` | |
| Contacts | `people` | |
| Calls | `call` | |
| Settings | `settings` | |
| Search | `search` | |
| Ellipsis | `ellipsis-horizontal` | |
| Schedule | `calendar-outline` | |
| Silent | `volume-mute-outline` | |
| Premium star | custom SVG | Gradient fill |
| Broadcast | custom SVG | |

## 8. Platform Notes

- **Theming**: Wrap the app root in `<TelegramThemeProvider>`. Let the user pick accent color from Settings; re-render cascades automatically.
- **OLED mode**: Offer an OLED toggle in Settings that swaps canvas to `#000000`. On AMOLED hardware this saves significant battery.
- **Custom chat backgrounds**: Implement as a full-bleed `<ImageBackground>` (or gradient) below the `FlatList` of messages. Store the chosen bg per-chat ID.
- **Blur**: Use `expo-blur` on the tab bar, nav bar, and floating voice pill.
- **Safe area**: Wrap all screens in `SafeAreaView` from `react-native-safe-area-context`; the voice mini-player sits just below the nav bar safe area.
- **Dynamic Type**: React Native font scaling respects user settings; set `allowFontScaling={false}` on the 10-11pt tab labels and inline timestamps.
- **RTL**: React Native handles RTL automatically for flex direction; make sure the bubble tail corner (`borderBottomRight/LeftRadius`) is swapped for RTL locales.
- **Lottie animations**: Respect `AccessibilityInfo.isReduceMotionEnabled()` — fall back to first-frame PNG when reduce motion is on.
- **Inline Markdown**: Parse `**bold**`, `__italic__`, `||spoiler||`, `` `code` `` with a small parser; render spoilers with a blurred mask that taps to reveal.
- **Gesture handler**: `react-native-gesture-handler` must wrap the root layout (`<GestureHandlerRootView>`).
