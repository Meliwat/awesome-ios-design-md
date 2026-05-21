# Quora (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Quora's visual language into paste-ready Expo / React Native code: a design-token module, the serif/sans split, and the signature vote pill.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:  '#FFFFFF',
  surface: '#F7F7F8',
  divider: '#E0E0E0',

  textPrimary:   '#282829',
  textSecondary: '#636466',
  textTertiary:  '#9A9A9C',

  red:        '#B92B27',
  redPressed: '#9E1F1B',
  redWash:    '#FBEAEA',
  upvote:     '#2E69FF',
  upvoteWash: '#EAF0FF',
  success:    '#1FA463',
} as const;

export type QuoraColor = keyof typeof colors;
```

## 2. Typography

The split is the brand: **Georgia for questions**, **Inter for answers / UI**. Georgia is a system serif on iOS; load Inter via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import { Platform } from 'react-native';
import type { TextStyle } from 'react-native';

// Georgia ships on iOS; Android fallback to 'serif'
const serif = Platform.select({ ios: 'Georgia', default: 'serif' });

const sans = { color: '#282829' } satisfies TextStyle;

export const typography = {
  // Questions — serif
  questionDetail:  { fontFamily: serif, fontWeight: '700' as const, fontSize: 26, lineHeight: 34, letterSpacing: -0.2, color: '#282829' },
  questionCard:    { fontFamily: serif, fontWeight: '700' as const, fontSize: 19, lineHeight: 26, letterSpacing: -0.1, color: '#282829' },
  questionRelated: { fontFamily: serif, fontWeight: '400' as const, fontSize: 16, lineHeight: 22, color: '#282829' },
  questionPrompt:  { fontFamily: serif, fontWeight: '700' as const, fontSize: 17, lineHeight: 23, color: '#282829' },

  // Answers / UI — Inter
  answerBody:  { ...sans, fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 26 }, // 1.6
  answerLead:  { ...sans, fontFamily: 'Inter-Regular',  fontSize: 17, lineHeight: 27 },
  credential:  {          fontFamily: 'Inter-SemiBold', fontSize: 13, lineHeight: 18, color: '#636466' },
  authorName:  { ...sans, fontFamily: 'Inter-Bold',     fontSize: 15, lineHeight: 20 },
  section:     { ...sans, fontFamily: 'Inter-Bold',     fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  meta:        {          fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 18, color: '#636466' },
  voteCount:   {          fontFamily: 'Inter-SemiBold', fontSize: 14, lineHeight: 14, color: '#636466' },
  button:      { fontFamily: 'Inter-Bold', fontSize: 15, lineHeight: 15, letterSpacing: 0.1, color: '#FFFFFF' },
  buttonSec:   { fontFamily: 'Inter-SemiBold', fontSize: 14, lineHeight: 14, color: '#282829' },
  tab:         { fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 12, letterSpacing: 0.1 },
  segment:     { fontFamily: 'Inter-Bold', fontSize: 14, lineHeight: 14, color: '#636466' },
  caption:     { fontFamily: 'Inter-Regular', fontSize: 12, lineHeight: 16, color: '#636466' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### The Serif/Sans Split — Question Feed Card

```tsx
// components/QuestionFeedCard.tsx
import { Pressable, Text, View, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { VotePill } from './VotePill';

export function QuestionFeedCard({
  authorName, credential, question, answerPreview,
}: { authorName: string; credential: string; question: string; answerPreview: string }) {
  return (
    <View style={styles.card}>
      <View style={styles.authorRow}>
        <View style={styles.avatar} />
        <View style={{ flex: 1 }}>
          <Text style={typography.authorName}>{authorName}</Text>
          <Text style={typography.credential}>{credential}</Text>
        </View>
      </View>

      {/* QUESTION — serif, always */}
      <Text style={typography.questionCard}>{question}</Text>

      {/* ANSWER — Inter, always, 3-line clamp + (more) */}
      <Text style={typography.answerBody} numberOfLines={3}>
        {answerPreview}
        <Text style={{ color: colors.upvote }}> (more)</Text>
      </Text>

      <View style={styles.footer}>
        <VotePill count="1.2K" />
        <Text style={[typography.buttonSec, { color: colors.textSecondary }]}>💬 84</Text>
        <Text style={[typography.buttonSec, { color: colors.textSecondary }]}>Share</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  card:      { backgroundColor: colors.canvas, padding: 16 },
  authorRow: { flexDirection: 'row', alignItems: 'center', gap: 8, marginBottom: 12 },
  avatar:    { width: 24, height: 24, borderRadius: 12, backgroundColor: colors.surface },
  footer:    { flexDirection: 'row', alignItems: 'center', gap: 24, marginTop: 12 },
});
```

### Upvote / Downvote Pill (Signature)

```tsx
// components/VotePill.tsx
import { useState } from 'react';
import { Pressable, Text, View, StyleSheet } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Vote = 'none' | 'up' | 'down';

export function VotePill({ count }: { count: string }) {
  const [vote, setVote] = useState<Vote>('none');
  const tap = (next: Vote) => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    setVote(v => (v === next ? 'none' : next));
  };
  const upActive = vote === 'up';
  const downActive = vote === 'down';

  return (
    <View style={styles.pill}>
      <Pressable
        onPress={() => tap('up')}
        style={[styles.seg, upActive && { backgroundColor: colors.upvoteWash }]}
      >
        <Text style={[styles.arrow, { color: upActive ? colors.upvote : colors.textSecondary }]}>
          {upActive ? '▲' : '△'}
        </Text>
        <Text style={[typography.voteCount, upActive && { color: colors.upvote }]}>{count}</Text>
      </Pressable>
      <View style={styles.vline} />
      <Pressable onPress={() => tap('down')} style={styles.seg}>
        {/* downvote stays neutral gray — never red */}
        <Text style={[styles.arrow, { color: colors.textSecondary }]}>
          {downActive ? '▼' : '▽'}
        </Text>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  pill:  { flexDirection: 'row', alignItems: 'center', height: 32, borderRadius: 16,
           borderWidth: 1, borderColor: colors.divider, overflow: 'hidden', backgroundColor: colors.canvas },
  seg:   { flexDirection: 'row', alignItems: 'center', gap: 6, paddingHorizontal: 12, height: 32 },
  vline: { width: 1, height: 20, backgroundColor: colors.divider },
  arrow: { fontSize: 13, fontWeight: '700' },
});
```

### Primary "Answer" CTA

```tsx
// components/AnswerButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AnswerButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 6,
        paddingVertical: 11, paddingHorizontal: 24, borderRadius: 6,
        backgroundColor: pressed ? colors.redPressed : colors.red,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={{ color: '#FFF', fontSize: 15 }}>✎</Text>
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Answer Detail Block (Credential Byline)

```tsx
// components/AnswerDetailBlock.tsx
import { Text, View, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { VotePill } from './VotePill';

export function AnswerDetailBlock({
  authorName, credential, timestamp, body,
}: { authorName: string; credential: string; timestamp: string; body: string }) {
  return (
    <View style={styles.block}>
      <View style={styles.authorRow}>
        <View style={styles.avatar} />
        <View style={{ flex: 1 }}>
          <Text style={typography.authorName}>{authorName}</Text>
          <Text style={typography.credential}>{credential}</Text>
          <Text style={typography.meta}>{timestamp}</Text>
        </View>
      </View>
      <Text style={typography.answerBody}>{body}</Text>
      <View style={styles.footer}>
        <VotePill count="3.4K" />
        <Text style={[typography.buttonSec, { color: colors.textSecondary }]}>💬 212</Text>
        <Text style={[typography.buttonSec, { color: colors.textSecondary }]}>Bookmark</Text>
      </View>
      <View style={styles.divider} />
    </View>
  );
}

const styles = StyleSheet.create({
  block:     { backgroundColor: colors.canvas, padding: 16, gap: 14 },
  authorRow: { flexDirection: 'row', gap: 12 },
  avatar:    { width: 40, height: 40, borderRadius: 20, backgroundColor: colors.surface },
  footer:    { flexDirection: 'row', alignItems: 'center', gap: 24 },
  divider:   { height: 1, backgroundColor: colors.divider, marginTop: 6 },
});
```

## 4. Spaces Carousel

```tsx
// components/SpacesRow.tsx
import { ScrollView, Text, View, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SpacesRow({ spaces }: { spaces: { name: string; members: string; banner: string }[] }) {
  return (
    <View>
      <View style={styles.header}>
        <Text style={typography.section}>Spaces for you</Text>
        <Text style={[typography.buttonSec, { color: colors.upvote }]}>See all</Text>
      </View>
      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={styles.row}>
        {spaces.map(s => (
          <View key={s.name} style={styles.tile}>
            <View style={[styles.banner, { backgroundColor: s.banner }]} />
            <View style={{ padding: 10, gap: 4 }}>
              <Text style={typography.authorName}>{s.name}</Text>
              <Text style={typography.credential}>{s.members}</Text>
            </View>
          </View>
        ))}
      </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  header: { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center', paddingHorizontal: 16, marginBottom: 12 },
  row:    { paddingHorizontal: 16, gap: 12 },
  tile:   { width: 140, borderRadius: 10, borderWidth: 1, borderColor: colors.divider, backgroundColor: colors.canvas, overflow: 'hidden' },
  banner: { height: 64 },
});
```

## 5. Navigation

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.textPrimary,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopColor: colors.divider, borderTopWidth: 1 },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="following" options={{ title: 'Following', tabBarIcon: ({ color }) => <Ionicons name="people-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="answer"    options={{ title: 'Answer',
        tabBarIcon: () => <Ionicons name="create" size={24} color={colors.red} /> }} />
      <Tabs.Screen name="spaces"    options={{ title: 'Spaces',    tabBarIcon: ({ color }) => <Ionicons name="albums-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="notifs"    options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Ionicons name="notifications-outline" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Upvote — light haptic; animate wash + count
import Animated, { useAnimatedStyle, withTiming } from 'react-native-reanimated';
const onUpvote = () => {
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
  // toggle vote → wash fades in over 200ms, count text swaps
};

// Card → detail: expo-router default crossfade + slide
// Composer present: push /compose as a modal with presentation: 'modal'

// Follow toggle: animate border color + label crossfade (LayoutAnimation or Reanimated)
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Quora's glyphs:

| Purpose | Ionicons |
|---------|----------|
| Upvote | `caret-up-outline` / `caret-up` |
| Downvote | `caret-down-outline` / `caret-down` |
| Comment | `chatbubble-outline` |
| Share | `share-outline` |
| Bookmark | `bookmark-outline` / `bookmark` |
| More | `ellipsis-horizontal` |
| Answer / Compose | `create-outline` / `create` |
| Search | `search-outline` |
| Home | `home-outline` / `home` |
| Following | `people-outline` |
| Spaces | `albums-outline` |
| Notifications | `notifications-outline` / `notifications` |

## 8. Platform Notes

- **Serif fallback**: `Georgia` is iOS-only. On Android use `'serif'` (Noto Serif) so the question/answer split survives cross-platform — never collapse both to one family
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` — the canvas is white
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the bottom nav respects the home indicator
- **Reading line-height**: enforce `lineHeight: 26` on 16pt answer body — RN does not infer 1.6 automatically
- **Dynamic Type**: RN respects font scaling by default; set `allowFontScaling={false}` on vote counts and tab labels only
- **Accessibility**: group the question and answer as distinct `accessibilityRole="text"` blocks; label the vote pill segments separately and never expose a downvote count
