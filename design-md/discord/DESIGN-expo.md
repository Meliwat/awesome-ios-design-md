# Discord (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Discord's visual language into paste-ready Expo / React Native code: a design-token module, themed components, Reanimated + Haptics snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, `@expo/vector-icons`, and `react-native-reanimated` v3. Use `react-native-gesture-handler` for the swipe drawer.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  blurple:        '#5865F2',
  blurplePressed: '#4752C4',
  blurpleLegacy:  '#7289DA',

  // Three-gray surface system (dark)
  serverRail:     '#1E1F22',
  channelList:    '#2B2D31',
  chatCanvas:     '#313338',
  surface2:       '#383A40',
  divider:        '#3F4147',
  rowHover:       '#2E3035',
  activeChannelBg:'#404249',

  // Text
  textPrimary:   '#F2F3F5',
  textSecondary: '#B5BAC1',
  textMuted:     '#949BA4',
  textLink:      '#00A8FC',
  textDisabled:  '#5D6069',

  // Status
  onlineGreen:     '#23A55A',
  idleYellow:      '#F0B232',
  dndRed:          '#F23F43',
  offlineGray:     '#80848E',
  streamingPurple: '#593695',

  // Destructive / Brand
  destructiveRed: '#DA373C',
  boostPink:      '#EB459E',
} as const;

export const nitroGradient = ['#5865F2', '#EB459E'] as const;

export type DCColor = keyof typeof colors;
```

## 2. Typography

gg sans is proprietary — bundle via `expo-font` if you have license; otherwise fall back to system (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'ggSans-Regular':  require('../assets/fonts/ggSans-Regular.ttf'),
    'ggSans-Medium':   require('../assets/fonts/ggSans-Medium.ttf'),
    'ggSans-Semibold': require('../assets/fonts/ggSans-Semibold.ttf'),
    'ggSans-Bold':     require('../assets/fonts/ggSans-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const family = 'ggSans-Regular';
const familyMedium = 'ggSans-Medium';
const familySemibold = 'ggSans-Semibold';
const familyBold = 'ggSans-Bold';

export const typography = {
  screenTitle:     { fontFamily: familyBold,     fontSize: 20, lineHeight: 24, color: '#F2F3F5' },
  channelActive:   { fontFamily: familySemibold, fontSize: 16, lineHeight: 20, color: '#F2F3F5' },
  channelInactive: { fontFamily: familyMedium,   fontSize: 16, lineHeight: 20, color: '#949BA4' },
  username:        { fontFamily: familySemibold, fontSize: 16, lineHeight: 20 },
  messageBody:     { fontFamily: family,         fontSize: 16, lineHeight: 22, color: '#F2F3F5' },
  timestamp:       { fontFamily: familyMedium,   fontSize: 12, lineHeight: 14, color: '#949BA4' },
  replyContext:    { fontFamily: family,         fontSize: 14, lineHeight: 18, color: '#B5BAC1' },
  systemMessage:   { fontFamily: familyMedium,   fontSize: 14, lineHeight: 18, color: '#B5BAC1' },
  sectionLabel:    { fontFamily: familyBold,     fontSize: 12, lineHeight: 14, color: '#949BA4', letterSpacing: 0.5, textTransform: 'uppercase' as const },
  memberName:      { fontFamily: familyMedium,   fontSize: 14, lineHeight: 17 },
  button:          { fontFamily: familyMedium,   fontSize: 14, lineHeight: 18, color: '#FFF' },
  tabLabel:        { fontFamily: familySemibold, fontSize: 10, lineHeight: 12 },
  code:            { fontFamily: 'Menlo',        fontSize: 14, lineHeight: 20, color: '#F2F3F5' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Server Rail

```tsx
// components/ServerRail.tsx
import { ScrollView, View, Text, Pressable, Image, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import Animated, { useAnimatedStyle, withSpring } from 'react-native-reanimated';
import { colors, nitroGradient } from '../theme/colors';

type Server = {
  id: string;
  name: string;
  imageUri?: string;
  initials: string;
  unreadCount: number;
  mentionCount: number;
};

export function ServerRail({ servers, activeId, onSelect }: {
  servers: Server[];
  activeId: string | null;
  onSelect: (id: string) => void;
}) {
  return (
    <ScrollView
      style={styles.rail}
      contentContainerStyle={styles.content}
      showsVerticalScrollIndicator={false}
    >
      <HomeButton />
      <View style={styles.separator} />
      {servers.map((s) => (
        <ServerIcon key={s.id} server={s} active={s.id === activeId} onPress={() => onSelect(s.id)} />
      ))}
      <View style={{ flex: 1, minHeight: 16 }} />
      <AddServerButton />
      <ExploreButton />
    </ScrollView>
  );
}

function ServerIcon({ server, active, onPress }: {
  server: Server; active: boolean; onPress: () => void;
}) {
  const showUnreadDot = !active && server.unreadCount > 0;

  return (
    <View style={styles.iconRow}>
      <View style={[
        styles.indicator,
        active && { height: 40 },
        !active && showUnreadDot && { height: 8 },
      ]} />
      <Pressable onPress={onPress} style={styles.iconPressable}>
        <View style={[
          styles.iconBox,
          { borderRadius: active ? 12 : 16 },
        ]}>
          {server.imageUri ? (
            <Image source={{ uri: server.imageUri }} style={styles.iconImage} />
          ) : (
            <View style={styles.iconFallback}>
              <Text style={styles.iconInitials}>{server.initials}</Text>
            </View>
          )}
        </View>
        {server.mentionCount > 0 && (
          <View style={styles.mentionPill}>
            <Text style={styles.mentionText}>{Math.min(server.mentionCount, 99)}</Text>
          </View>
        )}
      </Pressable>
    </View>
  );
}

function HomeButton() {
  return (
    <LinearGradient colors={[...nitroGradient]} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={styles.home}>
      <Ionicons name="home" size={22} color="#FFF" />
    </LinearGradient>
  );
}

function AddServerButton() {
  return (
    <View style={styles.ctaButton}>
      <Ionicons name="add" size={24} color={colors.onlineGreen} />
    </View>
  );
}

function ExploreButton() {
  return (
    <View style={styles.ctaButton}>
      <Ionicons name="compass" size={22} color={colors.onlineGreen} />
    </View>
  );
}

const styles = StyleSheet.create({
  rail:      { width: 72, backgroundColor: colors.serverRail },
  content:   { paddingVertical: 8, alignItems: 'center' },
  separator: { height: 2, width: 32, backgroundColor: colors.divider, marginVertical: 8, borderRadius: 1 },
  iconRow:   { flexDirection: 'row', alignItems: 'center', marginVertical: 4, width: '100%' },
  indicator: { width: 4, height: 0, backgroundColor: colors.textPrimary, borderTopRightRadius: 4, borderBottomRightRadius: 4 },
  iconPressable: { marginLeft: 8, position: 'relative' },
  iconBox:   { width: 48, height: 48, overflow: 'hidden' },
  iconImage: { width: 48, height: 48 },
  iconFallback: { width: 48, height: 48, backgroundColor: colors.blurple, alignItems: 'center', justifyContent: 'center' },
  iconInitials: { color: '#FFF', fontSize: 16, fontWeight: '600' },
  mentionPill: {
    position: 'absolute', right: -4, bottom: -4,
    minWidth: 18, height: 18, paddingHorizontal: 5, borderRadius: 9,
    backgroundColor: colors.dndRed, alignItems: 'center', justifyContent: 'center',
    borderWidth: 3, borderColor: colors.serverRail,
  },
  mentionText: { color: '#FFF', fontSize: 11, fontWeight: '700' },
  home:      { width: 48, height: 48, borderRadius: 16, alignItems: 'center', justifyContent: 'center', marginBottom: 4 },
  ctaButton: { width: 48, height: 48, borderRadius: 24, backgroundColor: colors.channelList, alignItems: 'center', justifyContent: 'center', marginVertical: 4 },
});
```

### Message Row (Role-Colored Username + Presence Dot)

```tsx
// components/MessageRow.tsx
import { View, Text, Image, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Presence = 'online' | 'idle' | 'dnd' | 'offline' | 'streaming';

export function MessageRow({
  avatarUri, username, roleColor, timestamp, message, presence, isGrouped,
}: {
  avatarUri: string;
  username: string;
  roleColor: string;
  timestamp: string;
  message: string;
  presence: Presence;
  isGrouped: boolean;
}) {
  const dotColor = {
    online: colors.onlineGreen,
    idle: colors.idleYellow,
    dnd: colors.dndRed,
    offline: colors.offlineGray,
    streaming: colors.streamingPurple,
  }[presence];

  return (
    <View style={[styles.row, isGrouped && { paddingVertical: 2 }]}>
      {isGrouped ? (
        <View style={{ width: 40, marginRight: 12 }} />
      ) : (
        <View style={styles.avatarWrap}>
          <Image source={{ uri: avatarUri }} style={styles.avatar} />
          <View style={[styles.presenceDot, { backgroundColor: dotColor, borderColor: colors.chatCanvas }]} />
        </View>
      )}
      <View style={{ flex: 1 }}>
        {!isGrouped && (
          <View style={styles.headerRow}>
            <Text style={[typography.username, { color: roleColor }]}>{username}</Text>
            <Text style={typography.timestamp}>{timestamp}</Text>
          </View>
        )}
        <Text style={typography.messageBody}>{message}</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  row: { flexDirection: 'row', alignItems: 'flex-start', paddingHorizontal: 16, paddingVertical: 8, gap: 12 },
  avatarWrap: { width: 40, height: 40, position: 'relative' },
  avatar: { width: 40, height: 40, borderRadius: 20 },
  presenceDot: { position: 'absolute', right: -2, bottom: -2, width: 14, height: 14, borderRadius: 7, borderWidth: 2.5 },
  headerRow: { flexDirection: 'row', alignItems: 'baseline', gap: 6, marginBottom: 2 },
});
```

### Compose Bar

```tsx
// components/ComposeBar.tsx
import { useState } from 'react';
import { View, TextInput, Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ComposeBar({ channelName, onSend }: { channelName: string; onSend: (t: string) => void }) {
  const [text, setText] = useState('');
  const [focused, setFocused] = useState(false);

  return (
    <View style={[styles.wrap, focused && styles.focused]}>
      <Pressable hitSlop={8}>
        <Ionicons name="add-circle" size={24} color={colors.textSecondary} />
      </Pressable>

      <TextInput
        placeholder={`Message ${channelName}`}
        placeholderTextColor={colors.textMuted}
        value={text}
        onChangeText={setText}
        onFocus={() => setFocused(true)}
        onBlur={() => setFocused(false)}
        multiline
        style={styles.input}
      />

      <Pressable hitSlop={8}>
        <Ionicons name="gift" size={22} color={colors.textSecondary} />
      </Pressable>
      <Pressable hitSlop={8}>
        <Ionicons name="image" size={22} color={colors.textSecondary} />
      </Pressable>
      <Pressable hitSlop={8}>
        <Ionicons name="happy" size={22} color={colors.textSecondary} />
      </Pressable>

      {text.length > 0 && (
        <Pressable
          onPress={() => {
            Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
            onSend(text);
            setText('');
          }}
          style={styles.sendCircle}
        >
          <Ionicons name="send" size={16} color="#FFF" />
        </Pressable>
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  wrap: {
    flexDirection: 'row', alignItems: 'center', gap: 10,
    backgroundColor: colors.surface2, borderRadius: 8,
    paddingHorizontal: 12, paddingVertical: 10,
    marginHorizontal: 12, marginBottom: 8,
    borderWidth: 2, borderColor: 'transparent',
  },
  focused: { borderColor: colors.blurple },
  input:   { flex: 1, color: colors.textPrimary, fontSize: 16, lineHeight: 20, maxHeight: 120 },
  sendCircle: {
    width: 32, height: 32, borderRadius: 16,
    backgroundColor: colors.blurple, alignItems: 'center', justifyContent: 'center',
  },
});
```

### Channel List Row

```tsx
// components/ChannelRow.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Type = 'text' | 'voice' | 'announcement';

export function ChannelRow({ name, type, isActive, unreadCount, mentionCount, onPress }: {
  name: string; type: Type; isActive: boolean; unreadCount: number; mentionCount: number; onPress: () => void;
}) {
  const icon = type === 'text' ? 'pound' : type === 'voice' ? 'volume-high' : 'megaphone';
  const textColor = isActive || unreadCount > 0 || mentionCount > 0 ? colors.textPrimary : colors.textMuted;
  const nameStyle = isActive ? typography.channelActive : typography.channelInactive;

  return (
    <Pressable onPress={onPress}>
      {isActive && <View style={styles.activeBar} />}
      <View style={[styles.row, isActive && styles.activeRow]}>
        <Ionicons name={icon as any} size={16} color={colors.textMuted} style={{ width: 20 }} />
        <Text style={[nameStyle, { color: textColor, flex: 1 }]}>{name}</Text>
        {mentionCount > 0 && (
          <View style={styles.mention}>
            <Text style={styles.mentionText}>{mentionCount}</Text>
          </View>
        )}
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row: { flexDirection: 'row', alignItems: 'center', paddingHorizontal: 8, paddingVertical: 6, height: 36, gap: 6, borderRadius: 4 },
  activeRow: { backgroundColor: colors.activeChannelBg },
  activeBar: { position: 'absolute', left: -4, top: 4, bottom: 4, width: 3, backgroundColor: colors.blurple, borderRadius: 2 },
  mention: { backgroundColor: colors.dndRed, minWidth: 18, height: 16, paddingHorizontal: 6, borderRadius: 8, alignItems: 'center', justifyContent: 'center' },
  mentionText: { color: '#FFF', fontSize: 12, fontWeight: '700' },
});
```

### Voice Speaking Ring

```tsx
// components/SpeakingRing.tsx
import { useEffect } from 'react';
import { View } from 'react-native';
import Animated, { useSharedValue, withRepeat, withTiming, useAnimatedStyle } from 'react-native-reanimated';
import { colors } from '../theme/colors';

export function SpeakingRing({ isActive, children }: { isActive: boolean; children: React.ReactNode }) {
  const scale = useSharedValue(1);

  useEffect(() => {
    if (isActive) {
      scale.value = withRepeat(withTiming(1.12, { duration: 300 }), -1, true);
    } else {
      scale.value = withTiming(1, { duration: 200 });
    }
  }, [isActive]);

  const ringStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
    opacity: isActive ? 1 : 0,
  }));

  return (
    <View style={{ position: 'relative' }}>
      {children}
      <Animated.View
        pointerEvents="none"
        style={[
          { position: 'absolute', inset: 0, borderWidth: 2.5, borderColor: colors.onlineGreen, borderRadius: 999 },
          ringStyle,
        ]}
      />
    </View>
  );
}
```

### Reaction Chip

```tsx
// components/ReactionChip.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function ReactionChip({ emoji, count, didYouReact, onPress }: {
  emoji: string; count: number; didYouReact: boolean; onPress: () => void;
}) {
  return (
    <Pressable
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
        onPress();
      }}
      style={[
        styles.chip,
        didYouReact && { backgroundColor: 'rgba(88,101,242,0.3)', borderColor: colors.blurple },
      ]}
    >
      <Text style={{ fontSize: 16 }}>{emoji}</Text>
      <Text style={styles.count}>{count}</Text>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  chip: {
    flexDirection: 'row', alignItems: 'center', gap: 4,
    paddingHorizontal: 8, paddingVertical: 4,
    borderRadius: 8,
    borderWidth: 1, borderColor: colors.divider,
    backgroundColor: colors.channelList,
  },
  count: { color: colors.textPrimary, fontSize: 13, fontWeight: '500' },
});
```

## 4. Three-Pane Swipe Navigation

```tsx
// components/DiscordShell.tsx
import { useState } from 'react';
import { Dimensions, View } from 'react-native';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useSharedValue, withSpring, useAnimatedStyle, runOnJS } from 'react-native-reanimated';
import { ServerRail } from './ServerRail';
import { ChannelListPane } from './ChannelListPane';
import { ChatPane } from './ChatPane';

const { width } = Dimensions.get('window');
const DRAWER_WIDTH = width * 0.85;

export function DiscordShell() {
  const [drawerOpen, setDrawerOpen] = useState(true);
  const tx = useSharedValue(drawerOpen ? DRAWER_WIDTH : 0);

  const chatStyle = useAnimatedStyle(() => ({ transform: [{ translateX: tx.value }] }));
  const drawerStyle = useAnimatedStyle(() => ({ transform: [{ translateX: tx.value - DRAWER_WIDTH }] }));

  const pan = Gesture.Pan()
    .onEnd((e) => {
      const shouldOpen = e.translationX > 60 || (drawerOpen && e.translationX > -60);
      tx.value = withSpring(shouldOpen ? DRAWER_WIDTH : 0);
      runOnJS(setDrawerOpen)(shouldOpen);
    });

  return (
    <GestureDetector gesture={pan}>
      <View style={{ flex: 1 }}>
        <Animated.View style={[{ flexDirection: 'row', position: 'absolute', top: 0, bottom: 0, width: DRAWER_WIDTH }, drawerStyle]}>
          <ServerRail servers={[]} activeId={null} onSelect={() => {}} />
          <ChannelListPane />
        </Animated.View>
        <Animated.View style={[{ flex: 1, width }, chatStyle]}>
          <ChatPane />
        </Animated.View>
      </View>
    </GestureDetector>
  );
}
```

## 5. Tab Bar (Mobile)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.textPrimary,
        tabBarInactiveTintColor: colors.textMuted,
        tabBarStyle: { backgroundColor: colors.serverRail, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontSize: 10, fontWeight: '600' },
      }}
    >
      <Tabs.Screen name="servers"      options={{ title: 'Servers',      tabBarIcon: ({ color }) => <Ionicons name="grid" size={22} color={color} /> }} />
      <Tabs.Screen name="messages"     options={{ title: 'Messages',     tabBarIcon: ({ color }) => <Ionicons name="chatbubbles" size={22} color={color} /> }} />
      <Tabs.Screen name="notifications" options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Ionicons name="notifications" size={22} color={color} />, tabBarBadge: 5 }} />
      <Tabs.Screen name="you"          options={{ title: 'You',          tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Server icon squircle morph
<View style={{ borderRadius: active ? 12 : 16 }} />
// Animate via Reanimated on press: useSharedValue + withTiming on radius if supported, else toggle style

// Active indicator bar height
const indicatorHeight = useSharedValue(active ? 40 : 0);
// Animated.View style={{ height: indicatorHeight.value, width: 4, backgroundColor: '#F2F3F5' }}

// Speaking ring pulse
scale.value = withRepeat(withTiming(1.12, { duration: 300 }), -1, true);

// Reaction chip press
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Drawer swipe
tx.value = withSpring(shouldOpen ? DRAWER_WIDTH : 0);

// Typing indicator three-dot bounce
// Use react-native-reanimated with staggered withRepeat(withSequence(withTiming(1), withTiming(0.3)))

// Message arrival
<Animated.View entering={FadeIn.duration(150).translateY(8)} />
```

## 7. Icon Library

Ionicons covers most of Discord's needs. Bundle custom SVGs for brand-specific icons (Nitro gradient star, Boost gem, Wumpus).

| Purpose | Ionicons |
|---------|----------|
| Text channel | `pound` |
| Voice channel | `volume-high` |
| Announcement | `megaphone` |
| Home / Discover | `home` |
| Add server | `add` |
| Explore | `compass` |
| Attach | `add-circle` |
| Gift | `gift` |
| GIF / media | `image` |
| Emoji | `happy` |
| Send | `send` |
| Pin | `pin` |
| Member list | `people` |
| Search | `search` |
| Notifications tab | `notifications` |
| Profile tab | `person-circle` |
| Settings | `settings` |
| Mic | `mic` / `mic-off` |
| Headphones | `headset` |
| Ellipsis | `ellipsis-horizontal` |

## 8. Platform Notes

- **Dark-first**: Discord ships dark-mode by default. Don't add a light-mode toggle unless explicitly requested.
- **No blur on chrome**: Discord's tab bar and nav are solid `#1E1F22` — not `.regularMaterial`. Don't use `expo-blur` for core chrome.
- **Role colors**: Usernames render in arbitrary hex (per-role). Store role color as a string on the user object; pass to `<MessageRow>` via `roleColor` prop. Validate contrast against `#313338` canvas — fall back to `#F2F3F5` on failure.
- **Markdown rendering**: Parse `**bold**`, `*italic*`, `__underline__`, `~~strike~~`, `` `code` ``, `||spoiler||`, `> quote` inline. Use `react-native-markdown-display` or build a custom tokenizer.
- **Spoiler tap-to-reveal**: Render as a dark `#2B2D31` rounded pill; tap fires `onReveal` and swaps to plain text.
- **Gesture handler**: Wrap the app in `<GestureHandlerRootView style={{ flex: 1 }}>` — required for the swipe drawer and message long-press menus.
- **Safe area**: Wrap screens in `SafeAreaView`. The top nav (`#1E1F22`) extends under the Dynamic Island.
- **Dynamic Type**: React Native respects user font-scaling; set `allowFontScaling={false}` only on 10pt tab labels and 12pt timestamps.
- **Accessibility**: `accessibilityRole="button"` on server icons; combine `accessibilityLabel` from username + role + presence for message rows; announce presence dot color as a semantic label.
- **Streaming indicator**: Replace the green dot with a purple dot + TV glyph when user is streaming — use a `<View>` with absolute-positioned inner `<Ionicons name="tv" />`.
