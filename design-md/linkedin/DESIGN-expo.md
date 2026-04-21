# LinkedIn (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates LinkedIn's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:        '#F3F2EF',
  cardSurface:   '#FFFFFF',
  elevated:      '#F9F9F9',
  divider:       '#E0DFDC',
  dividerSubtle: '#EDEDED',

  textPrimary:   'rgba(0,0,0,0.9)',
  textSecondary: 'rgba(0,0,0,0.6)',
  textTertiary:  'rgba(0,0,0,0.4)',

  blue:          '#0A66C2',
  bluePressed:   '#004182',
  blueSubtle:    '#E7F3FF',

  openToWork:    '#057642',
  premiumGold:   '#915907',
  premiumGoldHi: '#C37D16',

  reactLike:       '#0A66C2',
  reactCelebrate:  '#F5BB00',
  reactSupport:    '#B24020',
  reactLove:       '#DF704D',
  reactInsightful: '#E7A33E',
  reactFunny:      '#00A0DC',

  errorRed:      '#CC1016',
  successGreen:  '#0B5B4C',

  // Dark mode
  darkCanvas:    '#1B1F23',
  darkCard:      '#1D2226',
  darkElevated:  '#282E32',
  darkDivider:   '#38434F',
  darkBlue:      '#70B5F9',
} as const;

export type LinkedInColor = keyof typeof colors;
```

## 2. Typography

LinkedIn on iOS uses SF Pro (system). On Android + web, fall back to system defaults. No custom font bundling required.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: 'rgba(0,0,0,0.9)' } satisfies TextStyle;

export const typography = {
  profileName:    { ...base, fontSize: 24, fontWeight: '700', lineHeight: 28, letterSpacing: -0.2 },
  screenTitle:    { ...base, fontSize: 20, fontWeight: '700', lineHeight: 24, letterSpacing: -0.2 },
  sectionHeader:  { ...base, fontSize: 16, fontWeight: '700', lineHeight: 20, letterSpacing: -0.1 },

  postAuthor:     { ...base, fontSize: 14, fontWeight: '600', lineHeight: 18 },
  headline:       { color: 'rgba(0,0,0,0.6)', fontSize: 12, fontWeight: '400', lineHeight: 16 },
  postBody:       { ...base, fontSize: 14, fontWeight: '400', lineHeight: 21 },
  connectionDeg:  { color: 'rgba(0,0,0,0.6)', fontSize: 12, fontWeight: '400', lineHeight: 14 },
  meta:           { color: 'rgba(0,0,0,0.6)', fontSize: 12, fontWeight: '400', lineHeight: 14 },

  buttonPrimary:   { color: '#FFFFFF', fontSize: 16, fontWeight: '600', lineHeight: 20 },
  buttonSecondary: { color: '#0A66C2', fontSize: 14, fontWeight: '600', lineHeight: 18 },
  actionBar:       { color: 'rgba(0,0,0,0.6)', fontSize: 13, fontWeight: '600', lineHeight: 16 },

  tab:             { fontSize: 11, fontWeight: '500', lineHeight: 13, letterSpacing: 0.1 },
  badge:           { fontSize: 11, fontWeight: '700', lineHeight: 13, letterSpacing: 0.4 },
  inputPlaceholder:{ color: 'rgba(0,0,0,0.6)', fontSize: 14, fontWeight: '400' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary Pill Button (Connect / Follow / Apply)

```tsx
// components/PillButton.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PillButton({
  title,
  icon,
  variant = 'filled',
  onPress,
}: {
  title: string;
  icon?: keyof typeof Ionicons.glyphMap;
  variant?: 'filled' | 'outline';
  onPress: () => void;
}) {
  return (
    <Pressable
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
        onPress();
      }}
      style={({ pressed }) => ({
        paddingVertical: 8,
        paddingHorizontal: 16,
        borderRadius: 500,
        backgroundColor: variant === 'filled' ? (pressed ? colors.bluePressed : colors.blue) : 'transparent',
        borderWidth: variant === 'outline' ? 1 : 0,
        borderColor: colors.blue,
        transform: [{ scale: pressed ? 0.98 : 1 }],
        flexDirection: 'row',
        alignItems: 'center',
        gap: 6,
      })}
    >
      {icon ? (
        <Ionicons
          name={icon}
          size={16}
          color={variant === 'filled' ? '#FFFFFF' : colors.blue}
        />
      ) : null}
      <Text style={variant === 'filled' ? typography.buttonPrimary : typography.buttonSecondary}>
        {title}
      </Text>
    </Pressable>
  );
}
```

### Feed Post Card

```tsx
// components/FeedPostCard.tsx
import { Image, Pressable, StyleSheet, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { Avatar } from './Avatar';
import { ReactionFooterRow } from './ReactionFooterRow';
import { ActionBar } from './ActionBar';

export function FeedPostCard({
  authorName, connectionDegree, headline, timeAgo, body, mediaUri,
  isPremium = false, isOpenToWork = false,
}: {
  authorName: string;
  connectionDegree: string;   // "1st", "2nd", "3rd+"
  headline: string;
  timeAgo: string;
  body: string;
  mediaUri?: string;
  isPremium?: boolean;
  isOpenToWork?: boolean;
}) {
  return (
    <View style={styles.card}>
      <View style={styles.authorRow}>
        <Avatar size={56} isPremium={isPremium} isOpenToWork={isOpenToWork} />
        <View style={{ flex: 1, marginLeft: 12 }}>
          <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 4 }}>
            <Text style={typography.postAuthor}>{authorName}</Text>
            <Text style={typography.connectionDeg}>• {connectionDegree}</Text>
          </View>
          <Text style={typography.headline} numberOfLines={2}>{headline}</Text>
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginTop: 2 }}>
            <Text style={typography.meta}>{timeAgo}</Text>
            <Ionicons name="globe-outline" size={11} color={colors.textSecondary} />
          </View>
        </View>
        <Pressable hitSlop={12}>
          <Ionicons name="ellipsis-horizontal" size={20} color={colors.textSecondary} />
        </Pressable>
      </View>

      <Text style={[typography.postBody, { marginHorizontal: 16, marginTop: 12 }]}>{body}</Text>

      {mediaUri ? (
        <Image source={{ uri: mediaUri }} style={styles.media} resizeMode="cover" />
      ) : null}

      <ReactionFooterRow reactionCount={127} commentCount={14} />

      <View style={styles.divider} />

      <ActionBar />
    </View>
  );
}

const styles = StyleSheet.create({
  card: { backgroundColor: colors.cardSurface },
  authorRow: { flexDirection: 'row', paddingHorizontal: 16, paddingTop: 12, alignItems: 'flex-start' },
  media:     { marginTop: 12, width: '100%', height: 220, backgroundColor: colors.elevated },
  divider:   { height: 1, backgroundColor: colors.dividerSubtle, marginHorizontal: 16 },
});
```

### Avatar with Status Rings

```tsx
// components/Avatar.tsx
import { Image, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';

export function Avatar({
  size = 56, uri, isPremium, isOpenToWork,
}: { size?: number; uri?: string; isPremium?: boolean; isOpenToWork?: boolean }) {
  const ringSize = size + 8;

  return (
    <View style={{ width: ringSize, height: ringSize, alignItems: 'center', justifyContent: 'center' }}>
      {isPremium ? (
        <LinearGradient
          colors={[colors.premiumGold, colors.premiumGoldHi]}
          start={{ x: 0, y: 1 }}
          end={{ x: 0, y: 0 }}
          style={{ position: 'absolute', width: ringSize, height: ringSize, borderRadius: ringSize / 2 }}
        />
      ) : null}
      {isOpenToWork ? (
        <View
          style={{
            position: 'absolute',
            width: ringSize,
            height: ringSize,
            borderRadius: ringSize / 2,
            borderWidth: 4,
            borderColor: colors.openToWork,
          }}
        />
      ) : null}
      <Image
        source={uri ? { uri } : require('../assets/placeholder-avatar.png')}
        style={{ width: size, height: size, borderRadius: size / 2, backgroundColor: colors.elevated }}
      />
    </View>
  );
}
```

### The 6-Reaction Picker (Long-press)

```tsx
// components/ReactionPicker.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { FadeIn, useSharedValue, useAnimatedStyle, withSpring, withDelay } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

const REACTIONS = [
  { id: 'like',        icon: 'thumbs-up',   color: colors.reactLike },
  { id: 'celebrate',   icon: 'hand-right', color: colors.reactCelebrate },
  { id: 'support',     icon: 'heart-circle', color: colors.reactSupport },
  { id: 'love',        icon: 'heart',        color: colors.reactLove },
  { id: 'insightful',  icon: 'bulb',         color: colors.reactInsightful },
  { id: 'funny',       icon: 'happy',        color: colors.reactFunny },
] as const;

export function ReactionPicker({ onSelect }: { onSelect: (id: string) => void }) {
  return (
    <Animated.View entering={FadeIn.duration(200)} style={styles.container}>
      {REACTIONS.map((r, i) => (
        <ReactionIcon key={r.id} reaction={r} index={i} onSelect={onSelect} />
      ))}
    </Animated.View>
  );
}

function ReactionIcon({ reaction, index, onSelect }: { reaction: typeof REACTIONS[number]; index: number; onSelect: (id: string) => void }) {
  const scale = useSharedValue(0);
  scale.value = withDelay(index * 40, withSpring(1, { damping: 10, stiffness: 200 }));
  const animStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
        onSelect(reaction.id);
      }}
      style={{ width: 40, height: 40, alignItems: 'center', justifyContent: 'center' }}
    >
      <Animated.View style={animStyle}>
        <Ionicons name={reaction.icon as any} size={24} color={reaction.color} />
      </Animated.View>
    </Pressable>
  );
}

const styles = {
  container: {
    flexDirection: 'row' as const,
    gap: 12,
    paddingHorizontal: 8,
    paddingVertical: 4,
    backgroundColor: colors.cardSurface,
    borderRadius: 32,
    shadowColor: '#000',
    shadowOpacity: 0.12,
    shadowRadius: 16,
    shadowOffset: { width: 0, height: 4 },
    elevation: 6,
  },
};
```

### Reaction Footer (Stat Row)

```tsx
// components/ReactionFooterRow.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ReactionFooterRow({ reactionCount, commentCount }: { reactionCount: number; commentCount: number }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, paddingVertical: 8 }}>
      <View style={{ flexDirection: 'row' }}>
        <Bubble color={colors.reactLike} icon="thumbs-up" />
        <Bubble color={colors.reactCelebrate} icon="hand-right" offset />
        <Bubble color={colors.reactLove} icon="heart" offset />
      </View>
      <Text style={[typography.meta, { marginLeft: 6 }]}>{reactionCount}</Text>
      <View style={{ flex: 1 }} />
      <Text style={typography.meta}>{commentCount} comments</Text>
    </View>
  );
}

function Bubble({ color, icon, offset = false }: { color: string; icon: any; offset?: boolean }) {
  return (
    <View
      style={{
        width: 20, height: 20, borderRadius: 10,
        backgroundColor: color,
        alignItems: 'center', justifyContent: 'center',
        borderWidth: 2, borderColor: colors.cardSurface,
        marginLeft: offset ? -8 : 0,
      }}
    >
      <Ionicons name={icon} size={10} color="#FFFFFF" />
    </View>
  );
}
```

## 4. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:  colors.textPrimary,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: {
          backgroundColor: colors.cardSurface,
          borderTopWidth: 1,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontSize: 11, fontWeight: '500', letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"         options={{ title: 'Home',          tabBarIcon: ({ color }) => <Ionicons name="home"          size={24} color={color} /> }} />
      <Tabs.Screen name="network"       options={{ title: 'My Network',    tabBarIcon: ({ color }) => <Ionicons name="people"        size={24} color={color} /> }} />
      <Tabs.Screen name="post"          options={{ title: 'Post',          tabBarIcon: ({ color }) => <Ionicons name="add-circle"    size={28} color={color} /> }} />
      <Tabs.Screen name="notifications" options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Ionicons name="notifications" size={24} color={color} /> }} />
      <Tabs.Screen name="jobs"          options={{ title: 'Jobs',          tabBarIcon: ({ color }) => <Ionicons name="briefcase"     size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Top Nav (Home search)

```tsx
// components/TopNav.tsx
import { Image, Pressable, TextInput, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function TopNav({ onProfile, onMessaging }: { onProfile: () => void; onMessaging: () => void }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, padding: 12, backgroundColor: colors.cardSurface }}>
      <Pressable onPress={onProfile}>
        <Image source={require('../assets/avatar.png')} style={{ width: 28, height: 28, borderRadius: 14 }} />
      </Pressable>
      <View style={{ flex: 1, flexDirection: 'row', alignItems: 'center', gap: 8, backgroundColor: colors.dividerSubtle, borderRadius: 500, height: 40, paddingHorizontal: 12 }}>
        <Ionicons name="search" size={16} color={colors.textSecondary} />
        <TextInput placeholder="Search" placeholderTextColor={colors.textSecondary} style={{ flex: 1, fontSize: 14 }} />
      </View>
      <Pressable onPress={onMessaging} hitSlop={12}>
        <Ionicons name="chatbubble-ellipses-outline" size={22} color={colors.textPrimary} />
      </Pressable>
    </View>
  );
}
```

## 6. Motion

```tsx
// Like heart animation
const scale = useSharedValue(1);
const onLike = () => {
  scale.value = withSequence(withSpring(1.3, { damping: 8 }), withSpring(1));
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
};

// Long-press reaction picker — Reanimated Gesture Handler LongPress gesture
// minimumDuration: 400ms
// On start: opacity.value = withSpring(1); picker children enter with 40ms stagger

// Connection accepted confetti
// Use react-native-confetti-cannon or an expo-friendly Reanimated particle emitter
// Fire on button press + Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success)
```

## 7. Icon Library

Use `@expo/vector-icons` — ships with Ionicons, Feather, MaterialCommunityIcons. Map to LinkedIn's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Home | `home` / `home-outline` |
| My Network | `people` / `people-outline` |
| Post | `add-circle` / `add-circle-outline` |
| Notifications | `notifications` / `notifications-outline` |
| Jobs | `briefcase` / `briefcase-outline` |
| Search | `search` |
| Messaging | `chatbubble-ellipses` / `chatbubble-ellipses-outline` |
| Ellipsis | `ellipsis-horizontal` |
| Like | `thumbs-up` / `thumbs-up-outline` |
| Celebrate | `hand-right` |
| Love | `heart` |
| Support | `heart-circle` |
| Insightful | `bulb` |
| Funny | `happy` |
| Comment | `chatbubble-outline` |
| Repost | `repeat` |
| Send | `paper-plane` / `paper-plane-outline` |
| Globe (public post) | `globe-outline` |
| Connect | `person-add` |

## 8. Platform Notes

- **iOS-native feel**: LinkedIn relies on SF Pro — it's automatic on iOS. On Android the system font (Roboto) is the right fallback; don't swap in Source Sans.
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` globally — the cream canvas needs dark-content
- **Safe area**: wrap screens with `SafeAreaView` from `react-native-safe-area-context`; the top nav and bottom tab bar both need safe-area padding
- **Dynamic Type**: React Native respects `allowFontScaling` by default on `<Text>`; set `allowFontScaling={false}` on tab labels, connection-degree chips, and badge labels where layout breaks
- **Accessibility**: Group feed card author row with `accessible={true}` and a combined `accessibilityLabel`: "Sarah Chen, 1st connection, Principal Designer at Figma, posted 3 days ago"
- **Link underlines**: React Native `<Text>` does not underline by default — add `textDecorationLine: 'underline'` on links, @mentions, and #hashtags
- **Dark mode**: Use `useColorScheme()` from React Native to swap color tokens to `darkCanvas` / `darkCard` / `darkBlue` variants
