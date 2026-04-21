# Reddit (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Reddit's visual language into paste-ready Expo / React Native code: a design-token module, themed components, Reanimated + Haptics snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `expo-font`, `@expo/vector-icons`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  brandRed:        '#FF4500',
  alertRed:        '#FF585B',
  brandRedPressed: '#CC3700',

  // Vote semantic pair
  upvote:      '#FF8717',
  upvoteDark:  '#FFAA66',
  downvote:    '#7193FF',
  downvoteDark:'#9494FF',
  voteInactive:     '#878A8C',
  voteInactiveDark: '#818384',

  // Canvas & surfaces (light)
  canvasLight:        '#F6F7F8',
  canvasClassicLight: '#DAE0E6',
  cardLight:          '#FFFFFF',
  surface2Light:      '#F2F3F5',
  dividerLight:       '#EDEFF1',

  // Canvas & surfaces (dark)
  canvasDark:   '#1A1A1B',
  cardDark:     '#272729',
  surface2Dark: '#343536',
  dividerDark:  '#343536',

  // Text
  textPrimary:       '#1A1A1B',
  textSecondary:     '#7C7C7C',
  textTertiary:      '#AFAFAF',
  textPrimaryDark:   '#D7DADC',
  textSecondaryDark: '#818384',
  textLink:          '#0079D3',

  // Semantic
  successGreen:     '#46D160',
  warningYellow:    '#FFB000',
  nsfwYellow:       '#F3B200',
  gold:             '#FFB000',
  premiumGold:      '#FFD635',
  subredditDefault: '#0079D3',
} as const;

export type RDColor = keyof typeof colors;
```

## 2. Typography

Reddit Sans is proprietary — bundle via `expo-font` if you have license; otherwise fall back to system (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'RedditSans-Regular':  require('../assets/fonts/RedditSans-Regular.ttf'),
    'RedditSans-Medium':   require('../assets/fonts/RedditSans-Medium.ttf'),
    'RedditSans-SemiBold': require('../assets/fonts/RedditSans-SemiBold.ttf'),
    'RedditSans-Bold':     require('../assets/fonts/RedditSans-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const reg = 'RedditSans-Regular';
const med = 'RedditSans-Medium';
const sem = 'RedditSans-SemiBold';
const bld = 'RedditSans-Bold';

export const typography = {
  largeTitle:    { fontFamily: bld, fontSize: 24, lineHeight: 29, letterSpacing: -0.3 },
  postTitle:     { fontFamily: sem, fontSize: 18, lineHeight: 23, letterSpacing: -0.2 },
  body:          { fontFamily: reg, fontSize: 14, lineHeight: 21, letterSpacing: -0.1 },
  metadata:      { fontFamily: reg, fontSize: 12, lineHeight: 16, color: '#7C7C7C' },
  karma:         { fontFamily: bld, fontSize: 12, lineHeight: 12 },
  subredditPill: { fontFamily: sem, fontSize: 12, lineHeight: 14 },
  flairPill:     { fontFamily: sem, fontSize: 11, lineHeight: 13, letterSpacing: 0.2 },
  username:      { fontFamily: med, fontSize: 12, lineHeight: 14 },
  button:        { fontFamily: sem, fontSize: 14, lineHeight: 17 },
  tabLabel:      { fontFamily: med, fontSize: 11, lineHeight: 13 },
  navTitle:      { fontFamily: sem, fontSize: 17, lineHeight: 21 },
  sectionHeader: { fontFamily: bld, fontSize: 13, lineHeight: 16, letterSpacing: 0.5, textTransform: 'uppercase' as const },
  code:          { fontFamily: 'Menlo', fontSize: 13, lineHeight: 20 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Vote Column

```tsx
// components/VoteColumn.tsx
import { useState } from 'react';
import { View, Text, Pressable, StyleSheet } from 'react-native';
import Animated, { useSharedValue, withSpring, withSequence, useAnimatedStyle } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type VoteState = 'none' | 'up' | 'down';

export function VoteColumn({ baseKarma, state, onChange }: {
  baseKarma: number;
  state: VoteState;
  onChange: (next: VoteState) => void;
}) {
  const upScale = useSharedValue(1);
  const downScale = useSharedValue(1);

  const upStyle = useAnimatedStyle(() => ({ transform: [{ scale: upScale.value }] }));
  const downStyle = useAnimatedStyle(() => ({ transform: [{ scale: downScale.value }] }));

  const displayedKarma =
    state === 'up' ? baseKarma + 1 :
    state === 'down' ? baseKarma - 1 :
    baseKarma;

  const karmaColor =
    state === 'up' ? colors.upvote :
    state === 'down' ? colors.downvote :
    colors.voteInactive;

  const bounce = (sv: Animated.SharedValue<number>) => {
    sv.value = withSequence(withSpring(1.25, { damping: 5 }), withSpring(1, { damping: 12 }));
  };

  return (
    <View style={styles.col}>
      <Pressable
        hitSlop={8}
        onPress={() => {
          Haptics.selectionAsync();
          onChange(state === 'up' ? 'none' : 'up');
          bounce(upScale);
        }}
      >
        <Animated.View style={upStyle}>
          <Ionicons name="arrow-up" size={18} color={state === 'up' ? colors.upvote : colors.voteInactive} />
        </Animated.View>
      </Pressable>

      <Text style={[typography.karma, { color: karmaColor }]}>{displayedKarma}</Text>

      <Pressable
        hitSlop={8}
        onPress={() => {
          Haptics.selectionAsync();
          onChange(state === 'down' ? 'none' : 'down');
          bounce(downScale);
        }}
      >
        <Animated.View style={downStyle}>
          <Ionicons name="arrow-down" size={18} color={state === 'down' ? colors.downvote : colors.voteInactive} />
        </Animated.View>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  col: { width: 44, alignItems: 'center', gap: 4 },
});
```

### Post Card

```tsx
// components/PostCard.tsx
import { useState } from 'react';
import { View, Text, Image, Pressable, StyleSheet, ScrollView } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { VoteColumn, type VoteState } from './VoteColumn';
import { FlairPill, type Flair } from './FlairPill';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PostCard({
  subredditName, subredditAvatarUri, subredditAccent,
  timestamp, commentCount, title, body, flairs, mediaUri, baseKarma,
}: {
  subredditName: string;
  subredditAvatarUri?: string;
  subredditAccent: string;
  timestamp: string;
  commentCount: number;
  title: string;
  body?: string;
  flairs: Flair[];
  mediaUri?: string;
  baseKarma: number;
}) {
  const [vote, setVote] = useState<VoteState>('none');

  return (
    <View style={styles.card}>
      <View style={styles.header}>
        {subredditAvatarUri && (
          <Image source={{ uri: subredditAvatarUri }} style={styles.subIcon} />
        )}
        <Text style={typography.subredditPill}>r/{subredditName}</Text>
        <Text style={styles.dot}>•</Text>
        <Text style={typography.metadata}>{timestamp}</Text>
        <View style={{ flex: 1 }} />
        <Pressable style={[styles.joinPill, { backgroundColor: subredditAccent }]}>
          <Text style={[typography.button, { color: '#FFF' }]}>Join</Text>
        </Pressable>
      </View>

      <Text style={typography.postTitle} numberOfLines={3}>{title}</Text>

      {flairs.length > 0 && (
        <ScrollView horizontal showsHorizontalScrollIndicator={false}>
          <View style={{ flexDirection: 'row', gap: 6 }}>
            {flairs.map((f) => <FlairPill key={f.id} flair={f} />)}
          </View>
        </ScrollView>
      )}

      {mediaUri && (
        <Image source={{ uri: mediaUri }} style={styles.media} />
      )}

      {body && <Text style={typography.body} numberOfLines={4}>{body}</Text>}

      <View style={styles.actionRow}>
        <VoteColumn baseKarma={baseKarma} state={vote} onChange={setVote} />
        <View style={{ flex: 1 }} />
        <ActionIcon name="chatbubble-outline" label={`${commentCount}`} />
        <ActionIcon name="share-outline" />
        <ActionIcon name="bookmark-outline" />
        <ActionIcon name="ellipsis-horizontal" />
      </View>
    </View>
  );
}

function ActionIcon({ name, label }: { name: any; label?: string }) {
  return (
    <Pressable hitSlop={6} style={styles.action}>
      <Ionicons name={name} size={18} color={colors.textSecondary} />
      {label !== undefined && <Text style={typography.metadata}>{label}</Text>}
    </Pressable>
  );
}

const styles = StyleSheet.create({
  card: { backgroundColor: colors.cardLight, borderRadius: 16, padding: 12, marginHorizontal: 16, marginVertical: 4, gap: 10 },
  header: { flexDirection: 'row', alignItems: 'center', gap: 6 },
  subIcon: { width: 20, height: 20, borderRadius: 10, backgroundColor: colors.surface2Light },
  dot: { color: colors.textSecondary, fontSize: 12 },
  joinPill: { paddingHorizontal: 12, paddingVertical: 4, borderRadius: 16 },
  media: { width: '100%', aspectRatio: 16 / 9, borderRadius: 8, backgroundColor: colors.surface2Light },
  actionRow: { flexDirection: 'row', alignItems: 'center', gap: 8 },
  action: { flexDirection: 'row', alignItems: 'center', gap: 4, paddingHorizontal: 6, paddingVertical: 4 },
});
```

### Flair Pill

```tsx
// components/FlairPill.tsx
import { View, Text, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type Flair = {
  id: string;
  text: string;
  background: string;
  foreground: string;
  emoji?: string;
};

export function FlairPill({ flair }: { flair: Flair }) {
  return (
    <View style={[styles.pill, { backgroundColor: flair.background }]}>
      {flair.emoji && <Text style={{ fontSize: 11 }}>{flair.emoji}</Text>}
      <Text style={[typography.flairPill, { color: flair.foreground }]}>{flair.text}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  pill: { flexDirection: 'row', alignItems: 'center', gap: 4, paddingHorizontal: 6, paddingVertical: 3, borderRadius: 4 },
});

export const SystemFlairs = {
  nsfw:    { id: 'nsfw',    text: 'NSFW',    background: colors.nsfwYellow, foreground: '#000' },
  spoiler: { id: 'spoiler', text: 'SPOILER', background: '#000',             foreground: '#FFF' },
  oc:      { id: 'oc',      text: 'OC',      background: colors.successGreen, foreground: '#FFF' },
} satisfies Record<string, Flair>;
```

### Comment Row

```tsx
// components/CommentRow.tsx
import { useState } from 'react';
import { View, Text, Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type RDComment = {
  id: string;
  username: string;
  karma: number;
  timestamp: string;
  body: string;
  depth: number;
  replies: RDComment[];
};

export function CommentRow({ comment }: { comment: RDComment }) {
  const [collapsed, setCollapsed] = useState(false);

  return (
    <View style={styles.row}>
      {Array.from({ length: comment.depth }).map((_, i) => (
        <View key={i} style={styles.indent} />
      ))}
      <View style={styles.content}>
        <View style={styles.header}>
          <Pressable onPress={() => setCollapsed((c) => !c)} hitSlop={6}>
            <Ionicons name={collapsed ? 'add-circle-outline' : 'remove-circle-outline'} size={14} color={colors.textSecondary} />
          </Pressable>
          <Text style={typography.username}>u/{comment.username}</Text>
          <Text style={styles.dot}>•</Text>
          <Text style={typography.metadata}>{comment.timestamp}</Text>
          <Text style={styles.dot}>•</Text>
          <Text style={typography.metadata}>{comment.karma} karma</Text>
        </View>
        {!collapsed && (
          <>
            <Text style={typography.body}>{comment.body}</Text>
            <View style={styles.actions}>
              <Ionicons name="arrow-up" size={14} color={colors.textSecondary} />
              <Ionicons name="arrow-down" size={14} color={colors.textSecondary} />
              <Ionicons name="arrow-undo" size={14} color={colors.textSecondary} />
              <Ionicons name="share-outline" size={14} color={colors.textSecondary} />
              <Ionicons name="ellipsis-horizontal" size={14} color={colors.textSecondary} />
            </View>
          </>
        )}
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  row:     { flexDirection: 'row', paddingVertical: 10, paddingHorizontal: 16 },
  indent:  { width: 2, marginRight: 10, backgroundColor: colors.dividerLight },
  content: { flex: 1, gap: 6 },
  header:  { flexDirection: 'row', alignItems: 'center', gap: 6 },
  dot:     { color: colors.textSecondary, fontSize: 12 },
  actions: { flexDirection: 'row', alignItems: 'center', gap: 16, marginTop: 4 },
});
```

### Subreddit Banner

```tsx
// components/SubredditBanner.tsx
import { useState } from 'react';
import { View, Text, Image, Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SubredditBanner({
  subredditName, memberCount, bannerUri, avatarUri, accentColor,
}: {
  subredditName: string;
  memberCount: string;
  bannerUri?: string;
  avatarUri?: string;
  accentColor: string;
}) {
  const [joined, setJoined] = useState(false);

  return (
    <View>
      <View style={[styles.bannerWrap, { backgroundColor: accentColor }]}>
        {bannerUri && <Image source={{ uri: bannerUri }} style={StyleSheet.absoluteFill} />}
        <Pressable
          onPress={() => {
            Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
            setJoined((j) => !j);
          }}
          style={[styles.joinPill, joined ? { backgroundColor: '#FFF' } : { backgroundColor: accentColor }]}
        >
          <Ionicons name={joined ? 'checkmark' : 'add'} size={14} color={joined ? accentColor : '#FFF'} />
          <Text style={[typography.button, { color: joined ? accentColor : '#FFF' }]}>
            {joined ? 'Joined' : 'Join'}
          </Text>
        </Pressable>
      </View>
      <View style={styles.avatarWrap}>
        {avatarUri && <Image source={{ uri: avatarUri }} style={styles.avatar} />}
      </View>
      <View style={styles.body}>
        <Text style={typography.largeTitle}>r/{subredditName}</Text>
        <Text style={typography.metadata}>{memberCount}</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  bannerWrap: { height: 160, justifyContent: 'flex-start', alignItems: 'flex-end', padding: 16, overflow: 'hidden' },
  joinPill:   { flexDirection: 'row', alignItems: 'center', gap: 6, paddingHorizontal: 16, paddingVertical: 6, borderRadius: 500 },
  avatarWrap: { position: 'absolute', top: 124, left: 16, width: 72, height: 72, borderRadius: 36, backgroundColor: '#FFF', padding: 3 },
  avatar:     { width: 66, height: 66, borderRadius: 33 },
  body:       { paddingTop: 52, paddingHorizontal: 16, paddingBottom: 12, gap: 4 },
});
```

## 4. Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.brandRed,
        tabBarInactiveTintColor: colors.voteInactive,
        tabBarStyle: { backgroundColor: '#FFF', borderTopColor: colors.dividerLight },
        tabBarLabelStyle: { fontSize: 11, fontWeight: '500' },
      }}
    >
      <Tabs.Screen name="index"        options={{ title: 'Home',        tabBarIcon: ({ color }) => <Ionicons name="home" size={22} color={color} /> }} />
      <Tabs.Screen name="communities"  options={{ title: 'Communities', tabBarIcon: ({ color }) => <Ionicons name="people" size={22} color={color} /> }} />
      <Tabs.Screen name="create"       options={{ title: 'Create',      tabBarIcon: ({ color }) => <Ionicons name="add-circle" size={26} color={color} /> }} />
      <Tabs.Screen name="chat"         options={{ title: 'Chat',        tabBarIcon: ({ color }) => <Ionicons name="chatbubbles" size={22} color={color} /> }} />
      <Tabs.Screen name="inbox"        options={{ title: 'Inbox',       tabBarIcon: ({ color }) => <Ionicons name="notifications" size={22} color={color} />, tabBarBadge: 3 }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Vote bounce (see VoteColumn.tsx)
upScale.value = withSequence(withSpring(1.25, { damping: 5 }), withSpring(1, { damping: 12 }));

// Card press
// Use Pressable({ pressed }) → { opacity: pressed ? 0.95 : 1 }

// Comment collapse
// LayoutAnimation or Reanimated .withTiming on height

// Join success
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Infinite scroll card entry
<Animated.View entering={FadeIn.duration(150).translateY(8)} />

// Pull to refresh with Snoo
// Use ScrollView refreshControl with a custom RefreshControl component that shows a rotating Snoo PNG
```

## 6. Icon Library

| Purpose | Ionicons |
|---------|----------|
| Upvote | `arrow-up` |
| Downvote | `arrow-down` |
| Comment count | `chatbubble-outline` |
| Share | `share-outline` |
| Save | `bookmark-outline` / `bookmark` |
| More | `ellipsis-horizontal` |
| Reply | `arrow-undo` |
| Join / plus | `add` |
| Joined / check | `checkmark` |
| Home tab | `home` |
| Communities tab | `people` |
| Create tab | `add-circle` |
| Chat tab | `chatbubbles` |
| Inbox tab | `notifications` |
| Search | `search` |
| Sort | `swap-vertical` |
| Collapse comment | `remove-circle-outline` / `add-circle-outline` |

Custom Reddit brand glyphs (Snoo alien, Reddit wordmark) should be bundled as SVG assets.

## 7. Subreddit Accent Color (Dynamic Theming)

Subreddits set their own accent color. Pass via props or React Context.

```tsx
// theme/SubredditTheme.tsx
import { createContext, useContext } from 'react';

type SubredditTheme = {
  accentColor: string;
  displayName: string;
  isDark: boolean;
};

const SubredditThemeContext = createContext<SubredditTheme>({
  accentColor: '#0079D3',
  displayName: '',
  isDark: false,
});

export const useSubredditTheme = () => useContext(SubredditThemeContext);

export function SubredditThemeProvider({ theme, children }: {
  theme: SubredditTheme;
  children: React.ReactNode;
}) {
  return <SubredditThemeContext.Provider value={theme}>{children}</SubredditThemeContext.Provider>;
}
```

## 8. Platform Notes

- **No blur on chrome**: Reddit uses solid surfaces for nav and tab bar, not `expo-blur`. Keep it flat.
- **Card vs classic view**: Offer a toggle in settings. Card view = 16pt radius + 16pt side margins; Classic view = 0pt radius + full-bleed.
- **Markdown**: Parse `**bold**`, `*italic*`, `~~strike~~`, `> quote`, `` `code` ``, triple-backtick blocks, `[label](url)`. Use `react-native-markdown-display` or a custom parser.
- **Media**: Use `expo-image` for post media (better caching than the RN `Image`). For video autoplay, `expo-av` with `shouldPlay` and mute toggle.
- **Galleries**: For multi-image posts, use a `FlatList` horizontal paging mode with `pagingEnabled` + custom dot indicators.
- **Pull-to-refresh**: Use `RefreshControl` with `tintColor={colors.brandRed}`; optionally replace with a custom Snoo-spin indicator.
- **Safe area**: Wrap with `SafeAreaView` from `react-native-safe-area-context`.
- **Dynamic Type**: React Native respects font scaling; set `allowFontScaling={false}` on the 11-12pt metadata, karma, tab labels, and flair pills.
- **Subreddit accent color validation**: compute contrast ratio against `#FFFFFF`; if AA fails, darken the accent by 20% before applying to Join button white text.
- **Haptics**: `Haptics.selectionAsync()` on vote state change, `Haptics.notificationAsync(Success)` on Join, `Haptics.impactAsync(Medium)` on long-press card actions.
- **Snoo mascot**: bundle SVG asset for empty states (e.g. "No posts yet" on subscribed feed), error screens, and pull-to-refresh.
