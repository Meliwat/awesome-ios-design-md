# Facebook (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Facebook's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas (light)
  canvas:        '#F0F2F5',
  card:          '#FFFFFF',
  surfaceTint:   '#F7F8FA',
  divider:       '#E4E6EB',
  separator:     '#CED0D4',

  // Canvas (dark)
  darkCanvas:        '#18191A',
  darkCard:          '#242526',
  darkSurfaceTint:   '#3A3B3C',
  darkDivider:       '#3E4042',

  // Text
  textPrimaryLight:   '#050505',
  textPrimaryDark:    '#E4E6EB',
  textSecondaryLight: '#65676B',
  textSecondaryDark:  '#B0B3B8',
  textTertiary:       '#8A8D91',

  // Brand
  blue:        '#1877F2',
  bluePressed: '#0A5FC8',
  blueLight:   '#E7F3FF',

  // Reactions
  likeBlue:    '#1877F2',
  lovePink:    '#F3425F',
  careYellow:  '#F7B928',
  hahaYellow:  '#F7B928',
  wowYellow:   '#F7B928',
  sadYellow:   '#F7B928',
  angryOrange: '#E9710F',

  // Semantic
  liveRed:      '#FA3E3E',
  errorRed:     '#FA383E',
  successGreen: '#42B72A',
} as const;

export type FBColor = keyof typeof colors;
```

## 2. Typography

Facebook uses SF Pro on iOS (the platform default). No custom font loading is required — use system fonts directly.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
import { Platform } from 'react-native';

const system = Platform.select({ ios: undefined, default: 'System' }) as any;

const base = { color: '#050505' } satisfies TextStyle;

export const typography = {
  fLogo:         { fontSize: 22, fontWeight: '900' as const, color: '#FFFFFF', fontFamily: system },  // the "f" glyph
  screenTitle:   { ...base, fontSize: 24, fontWeight: '700' as const, lineHeight: 28 },
  sectionHeader: { ...base, fontSize: 20, fontWeight: '700' as const, lineHeight: 24 },
  displayName:   { ...base, fontSize: 15, fontWeight: '600' as const, lineHeight: 20 },
  postBody:      { ...base, fontSize: 15, fontWeight: '400' as const, lineHeight: 20 },
  postBodyLarge: { ...base, fontSize: 24, fontWeight: '600' as const, lineHeight: 30 },
  commentBody:   { ...base, fontSize: 14, fontWeight: '400' as const, lineHeight: 18 },
  commentName:   { ...base, fontSize: 13, fontWeight: '600' as const, lineHeight: 17 },
  timestamp:     { fontSize: 13, fontWeight: '400' as const, color: '#65676B', lineHeight: 17 },
  reactionCount: { fontSize: 13, fontWeight: '400' as const, color: '#65676B', lineHeight: 17 },
  actionLabel:   { fontSize: 15, fontWeight: '600' as const, color: '#65676B', lineHeight: 18 },
  cta:           { fontSize: 17, fontWeight: '600' as const, color: '#FFFFFF', lineHeight: 20 },
  tabLabel:      { fontSize: 11, fontWeight: '500' as const, lineHeight: 13 },
  sponsored:     { fontSize: 13, fontWeight: '400' as const, color: '#65676B', lineHeight: 17 },
  liveBadge:     { fontSize: 10, fontWeight: '700' as const, color: '#FFFFFF', letterSpacing: 0.5 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Post Card

```tsx
// components/PostCard.tsx
import { Image, Pressable, StyleSheet, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useState } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { ReactionsPopover, Reaction, reactionMeta } from './ReactionsPopover';

type Props = {
  displayName: string;
  timestamp: string;
  audience: 'Public' | 'Friends' | 'Only Me';
  avatarUri: string;
  body: string;
  mediaUri?: string;
};

export function PostCard(p: Props) {
  const [reaction, setReaction] = useState<Reaction | null>(null);
  const [showPopover, setShowPopover] = useState(false);

  return (
    <View style={styles.card}>
      {/* Header */}
      <View style={styles.header}>
        <Image source={{ uri: p.avatarUri }} style={styles.avatar} />
        <View style={{ flex: 1 }}>
          <Text style={typography.displayName}>{p.displayName}</Text>
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
            <Text style={typography.timestamp}>{p.timestamp}</Text>
            <Text style={typography.timestamp}>·</Text>
            <Ionicons name={p.audience === 'Public' ? 'earth' : 'people'} size={12} color={colors.textSecondaryLight} />
          </View>
        </View>
        <Pressable hitSlop={12}>
          <Ionicons name="ellipsis-horizontal" size={20} color={colors.textSecondaryLight} />
        </Pressable>
      </View>

      {/* Body */}
      <Text style={[typography.postBody, { paddingHorizontal: 12, paddingBottom: 12 }]}>{p.body}</Text>

      {/* Media */}
      {p.mediaUri && <Image source={{ uri: p.mediaUri }} style={styles.media} />}

      {/* Reaction summary */}
      <View style={styles.reactionSummary}>
        <View style={{ flexDirection: 'row' }}>
          <View style={[styles.reactionChip, { backgroundColor: colors.likeBlue }]}>
            <Ionicons name="thumbs-up" size={9} color="#fff" />
          </View>
          <View style={[styles.reactionChip, { backgroundColor: colors.lovePink, marginLeft: -4 }]}>
            <Ionicons name="heart" size={9} color="#fff" />
          </View>
        </View>
        <Text style={[typography.reactionCount, { flex: 1 }]}>  You, Sarah and 45 others</Text>
        <Text style={typography.reactionCount}>12 comments · 3 shares</Text>
      </View>

      <View style={styles.divider} />

      {/* Action row */}
      <View style={styles.actions}>
        <ActionButton
          icon={reaction ? reactionMeta[reaction].ion : 'thumbs-up-outline'}
          label={reaction ? reactionMeta[reaction].label : 'Like'}
          color={reaction ? reactionMeta[reaction].color : colors.textSecondaryLight}
          onPress={() => setReaction(reaction ? null : 'like')}
          onLongPress={() => setShowPopover(true)}
        />
        <ActionButton icon="chatbubble-outline" label="Comment" color={colors.textSecondaryLight} />
        <ActionButton icon="arrow-redo-outline" label="Share"   color={colors.textSecondaryLight} />
      </View>

      {showPopover && (
        <ReactionsPopover
          onSelect={r => {
            setReaction(r);
            setShowPopover(false);
          }}
          onCancel={() => setShowPopover(false)}
        />
      )}
    </View>
  );
}

function ActionButton({ icon, label, color, onPress, onLongPress }:
  { icon: any; label: string; color: string; onPress?: () => void; onLongPress?: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      onLongPress={onLongPress}
      delayLongPress={400}
      style={({ pressed }) => [styles.actionBtn, pressed && { backgroundColor: colors.divider }]}
    >
      <Ionicons name={icon} size={20} color={color} />
      <Text style={[typography.actionLabel, { color }]}>{label}</Text>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  card:            { backgroundColor: colors.card, borderRadius: 8, marginHorizontal: 8, marginVertical: 4 },
  header:          { flexDirection: 'row', alignItems: 'center', padding: 12, gap: 8 },
  avatar:          { width: 40, height: 40, borderRadius: 20, backgroundColor: colors.divider },
  media:           { width: '100%', aspectRatio: 1, backgroundColor: colors.divider },
  reactionSummary: { flexDirection: 'row', alignItems: 'center', paddingHorizontal: 12, paddingVertical: 8, gap: 6 },
  reactionChip:    { width: 18, height: 18, borderRadius: 9, alignItems: 'center', justifyContent: 'center' },
  divider:         { height: 1, backgroundColor: colors.divider },
  actions:         { flexDirection: 'row', paddingVertical: 4 },
  actionBtn:       { flex: 1, flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 6, paddingVertical: 10, borderRadius: 6 },
});
```

### Reactions Popover

```tsx
// components/ReactionsPopover.tsx
import { Pressable, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withDelay, withSequence } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useEffect, useState } from 'react';
import { colors } from '../theme/colors';

export type Reaction = 'like' | 'love' | 'care' | 'haha' | 'wow' | 'sad' | 'angry';

export const reactionMeta: Record<Reaction, { label: string; ion: any; color: string }> = {
  like:  { label: 'Like',  ion: 'thumbs-up',     color: colors.likeBlue },
  love:  { label: 'Love',  ion: 'heart',         color: colors.lovePink },
  care:  { label: 'Care',  ion: 'sparkles',      color: colors.careYellow },
  haha:  { label: 'Haha',  ion: 'happy',         color: colors.hahaYellow },
  wow:   { label: 'Wow',   ion: 'ellipse',       color: colors.wowYellow },
  sad:   { label: 'Sad',   ion: 'sad',           color: colors.sadYellow },
  angry: { label: 'Angry', ion: 'flame',         color: colors.angryOrange },
};

const ORDER: Reaction[] = ['like', 'love', 'care', 'haha', 'wow', 'sad', 'angry'];

export function ReactionsPopover({ onSelect, onCancel }: { onSelect: (r: Reaction) => void; onCancel: () => void }) {
  const [hovered, setHovered] = useState<number | null>(null);

  useEffect(() => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
  }, []);

  return (
    <View style={{
      position: 'absolute', bottom: 48, left: 16,
      flexDirection: 'row', padding: 6,
      borderRadius: 500, backgroundColor: colors.card,
      shadowColor: '#000', shadowOpacity: 0.2, shadowRadius: 16, shadowOffset: { width: 0, height: 4 },
    }}>
      {ORDER.map((r, i) => <ReactionBubble key={r} reaction={r} index={i} hovered={hovered === i}
                                            onHover={() => { if (hovered !== i) { setHovered(i); Haptics.selectionAsync(); } }}
                                            onSelect={() => {
                                              Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
                                              onSelect(r);
                                            }} />)}
    </View>
  );
}

function ReactionBubble({ reaction, index, hovered, onHover, onSelect }:
  { reaction: Reaction; index: number; hovered: boolean; onHover: () => void; onSelect: () => void }) {
  const scale = useSharedValue(0.5);
  useEffect(() => {
    scale.value = withDelay(index * 25, withSpring(1, { damping: 12 }));
  }, []);
  const hoverScale = useSharedValue(1);
  useEffect(() => {
    hoverScale.value = withSpring(hovered ? 1.3 : 1, { damping: 14 });
  }, [hovered]);

  const style = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value * hoverScale.value }],
  }));

  const meta = reactionMeta[reaction];

  return (
    <Pressable onPressIn={onHover} onPress={onSelect} hitSlop={4}>
      <Animated.View style={[{ width: 48, height: 48, alignItems: 'center', justifyContent: 'center' }, style]}>
        <Ionicons name={meta.ion} size={28} color={meta.color} />
      </Animated.View>
    </Pressable>
  );
}
```

### Top Nav Bar (with "f" logo)

```tsx
// components/TopNav.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TopNav() {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center',
      paddingHorizontal: 16, height: 56,
      backgroundColor: colors.card,
      borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      {/* Blue 'f' logo square */}
      <View style={{
        width: 36, height: 36, borderRadius: 8,
        backgroundColor: colors.blue,
        alignItems: 'center', justifyContent: 'center',
      }}>
        <Text style={typography.fLogo}>f</Text>
      </View>

      <View style={{ flex: 1 }} />

      <Pressable style={styles.circleBtn}><Ionicons name="search" size={18} color={colors.textPrimaryLight} /></Pressable>
      <Pressable style={[styles.circleBtn, { marginLeft: 8 }]}><Ionicons name="chatbubbles" size={18} color={colors.textPrimaryLight} /></Pressable>
    </View>
  );
}

const styles = {
  circleBtn: {
    width: 36, height: 36, borderRadius: 18,
    backgroundColor: colors.divider,
    alignItems: 'center' as const, justifyContent: 'center' as const,
  },
};
```

## 4. Facebook-Specific Feature: Reactions Popover (Full Drag-Select Pattern)

The `ReactionsPopover` component above is the core. To wire it up with a long-press + drag-to-hover gesture on the Like button, use `PanResponder` or `react-native-gesture-handler`:

```tsx
// components/LikeWithReactions.tsx
import { GestureDetector, Gesture } from 'react-native-gesture-handler';
import { runOnJS } from 'react-native-reanimated';
import { useState } from 'react';
import { View } from 'react-native';
import { ReactionsPopover, Reaction, reactionMeta } from './ReactionsPopover';

export function LikeWithReactions({ onReaction }: { onReaction: (r: Reaction | null) => void }) {
  const [showPopover, setShowPopover] = useState(false);
  const [reaction, setReaction] = useState<Reaction | null>(null);

  const longPress = Gesture.LongPress()
    .minDuration(400)
    .onStart(() => runOnJS(setShowPopover)(true));

  return (
    <GestureDetector gesture={longPress}>
      <View>
        {/* Your action button JSX */}
        {showPopover && (
          <ReactionsPopover
            onSelect={r => { setReaction(r); onReaction(r); setShowPopover(false); }}
            onCancel={() => setShowPopover(false)}
          />
        )}
      </View>
    </GestureDetector>
  );
}
```

## 5. Tab Bar

Facebook shows labels under every tab — not icon-only.

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.blue,
        tabBarInactiveTintColor: colors.textSecondaryLight,
        tabBarShowLabel: true,  // Facebook shows labels
        tabBarStyle: {
          backgroundColor: colors.card,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontSize: 11, fontWeight: '500' },
      }}
    >
      <Tabs.Screen name="index"         options={{ title: 'Home',          tabBarIcon: ({ color }) => <Ionicons name="home"          size={26} color={color} /> }} />
      <Tabs.Screen name="video"         options={{ title: 'Video',         tabBarIcon: ({ color }) => <Ionicons name="play-circle"   size={26} color={color} /> }} />
      <Tabs.Screen name="marketplace"   options={{ title: 'Marketplace',   tabBarIcon: ({ color }) => <Ionicons name="cart"          size={26} color={color} /> }} />
      <Tabs.Screen name="notifications" options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Ionicons name="notifications" size={26} color={color} />, tabBarBadge: 3 }} />
      <Tabs.Screen name="menu"          options={{ title: 'Menu',          tabBarIcon: ({ color }) => <Ionicons name="menu"          size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Like tap — scale bounce + color fill
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

function LikeButton({ isLiked, onToggle }: { isLiked: boolean; onToggle: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const onPress = () => {
    scale.value = withSequence(withSpring(0.9, { damping: 10 }), withSpring(1.15, { damping: 8 }), withSpring(1, { damping: 10 }));
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    onToggle();
  };

  return { onPress, style };
}

// Reactions popover appear — staggered spring (see ReactionsPopover above)

// Reaction selected — emoji swaps into Like slot
// Use LayoutAnimation or a cross-fade:
import { LayoutAnimation, Platform, UIManager } from 'react-native';
if (Platform.OS === 'android' && UIManager.setLayoutAnimationEnabledExperimental) {
  UIManager.setLayoutAnimationEnabledExperimental(true);
}
LayoutAnimation.configureNext(LayoutAnimation.Presets.easeInEaseOut);
```

## 7. Icon Library

Use `@expo/vector-icons` — ships with Ionicons. Map to Facebook's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Like (outline) | `thumbs-up-outline` |
| Like (filled) | `thumbs-up` |
| Love | `heart` |
| Care | `sparkles` |
| Haha | `happy` |
| Wow | `ellipse` (or custom emoji) |
| Sad | `sad` |
| Angry | `flame` (or custom emoji) |
| Comment | `chatbubble-outline` |
| Share | `arrow-redo-outline` |
| Overflow | `ellipsis-horizontal` |
| Search | `search` |
| Messenger | `chatbubbles` |
| Home | `home-outline` / `home` |
| Video | `play-circle-outline` / `play-circle` |
| Marketplace | `cart-outline` / `cart` |
| Notifications | `notifications-outline` / `notifications` |
| Menu | `menu` |
| Photo picker | `image-outline` |
| Live video | `radio-outline` |
| Audience Public | `earth` |
| Audience Friends | `people` |
| Add Friend | `person-add` |
| Friends | `person` (+ `checkmark-circle`) |

For authentic-looking emoji reactions, consider rendering them as actual emoji characters in `<Text>` (👍 ❤️ 🤗 😂 😮 😢 😠) rather than Ionicons, which are monochrome.

## 8. Platform Notes

- **Platform font**: Facebook uses SF Pro on iOS, which is React Native's default — no `useFonts` needed. On Android it falls back to Roboto, which is acceptable for a cross-platform build
- **Status bar**: set `<StatusBar style="dark" />` on light mode, `"light"` on dark — Facebook's canvas is soft gray, not pure white, so dark text on the bar is correct
- **Safe area**: wrap every screen in `SafeAreaView` from `react-native-safe-area-context`. The top nav sits inside the safe area; the feed starts immediately below
- **Dynamic Type**: React Native respects user font-scaling by default; set `allowFontScaling={false}` on tab labels (11pt is tight) and reaction counts (13pt layout)
- **Accessibility**: post cards should be wrapped with `accessible={true}` + `accessibilityRole="article"`. Reactions popover should be reachable without a long-press gesture — expose an alternative `accessibilityAction` for "Choose Reaction"
- **Cards + shadows**: Facebook cards rely on the gray canvas for separation, so don't add `elevation` on Android — it would add unwanted shadow. Use `borderRadius: 8` + `backgroundColor` only
- **Blue 'f' glyph**: render as styled `<Text>` inside a blue square `<View>`, or import the SVG. For perfect visual fidelity, use the official SVG (available in Facebook's brand toolkit)
- **Reactions emoji**: using Unicode emoji gives the most authentic feel (Apple's emoji set matches Facebook's reactions closely). Alternatively, use custom SVG icons for Care, Haha, Wow, Sad which don't map 1:1 to Ionicons
- **Long-press reactions**: `Gesture.LongPress()` from `react-native-gesture-handler` with 400ms minimum duration; combine with a `Gesture.Pan()` for drag-to-hover selection
- **Stories ring**: use `expo-linear-gradient` with `colors={['#C13584', '#E1306C', '#FD1D1D', '#F77737']}` masked to a ring shape around the avatar
