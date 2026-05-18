# Vero (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Vero's calm, true-black visual language into paste-ready Expo / React Native code: a design-token module, the brand gradient, themed components, and Reanimated snippets for the 7 post-type selector and chronological feed.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — primary)
  canvas:    '#000000',
  surface1:  '#0E0E10',
  surface2:  '#161618',
  divider:   '#232325',

  // Surfaces (light — rare parity)
  lightCanvas:   '#FFFFFF',
  lightSurface1: '#F6F6F7',
  lightDivider:  '#E4E4E6',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#9A9AA0',
  textTertiary:  '#5E5E64',

  // Brand
  veroTeal:      '#00C2B8',
  veroBlue:      '#0098E6',
  gradientA:     '#00D1C1',
  gradientB:     '#0079D3',
  tealPressed:   '#00A89F',

  // Ratings
  amber:   '#E8B23A',
  onAmber: '#1A1206',

  // Semantic
  success: '#2ECC71',
  error:   '#FF4D4F',
} as const;

export type VeroColor = keyof typeof colors;

// The single brand expression — use sparingly (wordmark, Post CTA, compose ring, active)
export const brandGradient = ['#00D1C1', '#0079D3'] as const;
```

## 2. Typography

Load **Manrope** via `expo-font` (SIL OFL — free).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Manrope-Regular':   require('../assets/fonts/Manrope-Regular.ttf'),
    'Manrope-Medium':    require('../assets/fonts/Manrope-Medium.ttf'),
    'Manrope-SemiBold':  require('../assets/fonts/Manrope-SemiBold.ttf'),
    'Manrope-Bold':      require('../assets/fonts/Manrope-Bold.ttf'),
    'Manrope-ExtraBold': require('../assets/fonts/Manrope-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  display:     { color: '#FFFFFF', fontFamily: 'Manrope-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  screenTitle: { color: '#FFFFFF', fontFamily: 'Manrope-Bold',      fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:     { color: '#FFFFFF', fontFamily: 'Manrope-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  author:      { color: '#FFFFFF', fontFamily: 'Manrope-Bold',      fontSize: 18, lineHeight: 24, letterSpacing: -0.1 },
  authorSm:    { color: '#FFFFFF', fontFamily: 'Manrope-Bold',      fontSize: 14, lineHeight: 18 },
  title:       { color: '#FFFFFF', fontFamily: 'Manrope-SemiBold',  fontSize: 15, lineHeight: 20 },
  caption:     { color: '#9A9AA0', fontFamily: 'Manrope-Medium',    fontSize: 14, lineHeight: 21 },
  bodyRead:    { color: '#9A9AA0', fontFamily: 'Manrope-Regular',   fontSize: 14, lineHeight: 22 },
  meta:        { color: '#5E5E64', fontFamily: 'Manrope-Regular',   fontSize: 13, lineHeight: 18 },
  button:      { color: '#FFFFFF', fontFamily: 'Manrope-Bold',      fontSize: 15, lineHeight: 15, letterSpacing: 0.2 },
  typeLabel:   { color: '#5E5E64', fontFamily: 'Manrope-SemiBold',  fontSize: 11, lineHeight: 11, letterSpacing: 0.4 },
  tab:         { color: '#5E5E64', fontFamily: 'Manrope-Medium',    fontSize: 10, lineHeight: 10 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### 7 Post-Type Selector

```tsx
// components/PostTypeSelector.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export const POST_TYPES = [
  { key: 'photo', label: 'Photo', icon: 'image-outline' },
  { key: 'video', label: 'Video', icon: 'videocam-outline' },
  { key: 'link',  label: 'Link',  icon: 'link-outline' },
  { key: 'music', label: 'Music', icon: 'musical-notes-outline' },
  { key: 'film',  label: 'Film',  icon: 'film-outline' },
  { key: 'book',  label: 'Book',  icon: 'book-outline' },
  { key: 'place', label: 'Place', icon: 'location-outline' },
] as const;

export function PostTypeSelector({
  selected, onSelect,
}: { selected: string; onSelect: (k: string) => void }) {
  return (
    <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 10 }}>
      {POST_TYPES.map(t => {
        const on = t.key === selected;
        return (
          <Pressable
            key={t.key}
            onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onSelect(t.key); }}
            style={{
              flexGrow: 1, flexBasis: 64, alignItems: 'center', gap: 7,
              paddingVertical: 12, paddingHorizontal: 4, borderRadius: 12,
              backgroundColor: on ? 'rgba(0,194,184,0.08)' : colors.surface1,
              borderWidth: 1, borderColor: on ? colors.veroTeal : colors.divider,
            }}
          >
            <Ionicons name={t.icon as any} size={20} color={on ? colors.veroTeal : colors.textTertiary} />
            <Text style={[typography.typeLabel, { color: on ? colors.veroTeal : colors.textTertiary }]}>{t.label}</Text>
          </Pressable>
        );
      })}
    </View>
  );
}
```

### Chronological Feed Post

```tsx
// components/FeedPost.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { brandGradient, colors } from '../theme/colors';
import { typography } from '../theme/typography';

const TYPE_ICON: Record<string, string> = {
  photo: 'image', video: 'videocam', link: 'link',
  music: 'musical-notes', film: 'film', book: 'book', place: 'location',
};

export function FeedPost({
  author, type, timeAgo, caption, likes, comments,
}: { author: string; type: string; timeAgo: string; caption: string; likes: number; comments: number }) {
  const [liked, setLiked] = useState(false);
  return (
    <View style={{ paddingHorizontal: 16, paddingTop: 16, paddingBottom: 14, borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10 }}>
        <LinearGradient colors={brandGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ width: 38, height: 38, borderRadius: 19 }} />
        <View style={{ flex: 1 }}>
          <Text style={typography.authorSm}>{author}</Text>
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5, marginTop: 2 }}>
            <Ionicons name={TYPE_ICON[type] as any} size={11} color={colors.veroTeal} />
            <Text style={{ fontFamily: 'Manrope-Bold', fontSize: 10, color: colors.veroTeal }}>
              {type[0].toUpperCase() + type.slice(1)}
            </Text>
            <Text style={typography.meta}>· {timeAgo}</Text>
          </View>
        </View>
        <Ionicons name="ellipsis-horizontal" size={18} color={colors.textTertiary} />
      </View>

      <LinearGradient colors={['#1C4A52', '#07181B']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ aspectRatio: 4 / 5, borderRadius: 10, marginTop: 12 }} />

      <Text style={[typography.caption, { marginTop: 12 }]}>{caption}</Text>

      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 22, marginTop: 14 }}>
        <Pressable
          onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); setLiked(v => !v); }}
          style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}
        >
          <Ionicons name={liked ? 'heart' : 'heart-outline'} size={19} color={liked ? colors.veroTeal : colors.textSecondary} />
          <Text style={{ fontFamily: 'Manrope-SemiBold', fontSize: 12, color: liked ? colors.veroTeal : colors.textSecondary }}>
            {likes + (liked ? 1 : 0)}
          </Text>
        </Pressable>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
          <Ionicons name="chatbubble-outline" size={19} color={colors.textSecondary} />
          <Text style={{ fontFamily: 'Manrope-SemiBold', fontSize: 12, color: colors.textSecondary }}>{comments}</Text>
        </View>
        <Ionicons name="share-outline" size={19} color={colors.textSecondary} />
      </View>
    </View>
  );
}
```

### Book / Film Inner Card

```tsx
// components/BookCard.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BookCard({ title, author, rating }: { title: string; author: string; rating: number }) {
  return (
    <View style={{
      flexDirection: 'row', gap: 12, padding: 12, borderRadius: 10,
      backgroundColor: colors.surface1, borderWidth: 1, borderColor: colors.divider,
    }}>
      <LinearGradient colors={['#C29A3A', '#7A5A18']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ width: 56, height: 80, borderRadius: 4 }} />
      <View style={{ flex: 1 }}>
        <Text style={typography.title}>{title}</Text>
        <Text style={[typography.meta, { color: colors.textSecondary, marginTop: 3 }]}>{author}</Text>
        <Text style={{ color: colors.amber, fontFamily: 'Manrope-SemiBold', fontSize: 11, letterSpacing: 1, marginTop: 4 }}>
          {'★'.repeat(rating)}{'☆'.repeat(5 - rating)}
        </Text>
      </View>
    </View>
  );
}
```

### Primary "Post" Button + Wordmark

```tsx
// components/VeroPostButton.tsx
import { Pressable, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import MaskedView from '@react-native-masked-view/masked-view';
import { brandGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function VeroPostButton({ onPress }: { onPress: () => void }) {
  return (
    <Pressable onPress={onPress}>
      {({ pressed }) => (
        <LinearGradient
          colors={brandGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ borderRadius: 999, paddingVertical: 13, paddingHorizontal: 28, opacity: pressed ? 0.94 : 1 }}
        >
          <Text style={typography.button}>Post</Text>
        </LinearGradient>
      )}
    </Pressable>
  );
}

export function VeroWordmark() {
  return (
    <MaskedView maskElement={
      <Text style={{ fontFamily: 'Manrope-ExtraBold', fontSize: 26, letterSpacing: 2 }}>VERO</Text>
    }>
      <LinearGradient colors={brandGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}>
        <Text style={{ fontFamily: 'Manrope-ExtraBold', fontSize: 26, letterSpacing: 2, opacity: 0 }}>VERO</Text>
      </LinearGradient>
    </MaskedView>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { BlurView } from 'expo-blur';
import { View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { brandGradient, colors } from '../../theme/colors';

function ComposeRing() {
  return (
    <View style={{ width: 27, height: 27, alignItems: 'center', justifyContent: 'center' }}>
      <LinearGradient colors={brandGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ position: 'absolute', width: 27, height: 27, borderRadius: 14 }} />
      <View style={{ position: 'absolute', width: 23, height: 23, borderRadius: 12, backgroundColor: colors.canvas }} />
      <Ionicons name="add" size={15} color={colors.veroTeal} />
    </View>
  );
}

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.veroTeal,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'rgba(0,0,0,0.96)' },
        tabBarBackground: () => <BlurView intensity={26} tint="dark" style={{ flex: 1 }} />,
        tabBarLabelStyle: { fontFamily: 'Manrope-Medium', fontSize: 10 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Feed',     tabBarIcon: ({ color }) => <Ionicons name="home" size={22} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="search" size={22} color={color} /> }} />
      <Tabs.Screen name="compose"  options={{ title: '',         tabBarIcon: () => <ComposeRing /> }} />
      <Tabs.Screen name="inbox"    options={{ title: 'Inbox',    tabBarIcon: ({ color }) => <Ionicons name="mail" size={22} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Post-type select — tint over 160ms (Reanimated interpolateColor or simple setState)
// Pressable + Haptics.impactAsync on press; border/content swap

// Like heart spring
import Animated, { useSharedValue, withSequence, withSpring } from 'react-native-reanimated';
scale.value = withSequence(withSpring(1.2, { damping: 6 }), withSpring(1));

// Compose sheet — slide up 300ms
// @gorhom/bottom-sheet or expo-router modal; default slide

// New posts insert at top — FadeIn only, never reshuffle
import { FadeIn } from 'react-native-reanimated';
// <Animated.View entering={FadeIn.duration(220)} />

// Tab switch — crossfade content 200ms; tint instant

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); // post-type select, like, connect
Haptics.selectionAsync();                               // tab change
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). For the gradient "VERO" wordmark use `@react-native-masked-view/masked-view` + `expo-linear-gradient`.

| Purpose | Ionicons |
|---------|----------|
| Feed | `home` / `home-outline` |
| Discover | `search` |
| Compose (center) | `add` (in a gradient ring) |
| Inbox | `mail` / `mail-outline` |
| Profile | `person-circle` / `person-circle-outline` |
| Post-type · Photo | `image-outline` |
| Post-type · Video | `videocam-outline` |
| Post-type · Link | `link-outline` |
| Post-type · Music | `musical-notes-outline` |
| Post-type · Film | `film-outline` |
| Post-type · Book | `book-outline` |
| Post-type · Place | `location-outline` |
| Like | `heart` / `heart-outline` |
| Comment | `chatbubble-outline` |
| Share | `share-outline` |
| More | `ellipsis-horizontal` |
| Notifications | `notifications-outline` |
| Rating star | `star` (or text ★) |

## 7. Platform Notes

- **Font choice**: Manrope is SIL OFL — free to bundle. Ship Regular → ExtraBold
- **True-black-first**: force `<StatusBar style="light" />`; set `userInterfaceStyle: "dark"` in `app.json`. The pure-black canvas is intentional for OLED — never substitute a charcoal; only inset cards use `#0E0E10`
- **Safe area**: wrap screens in `SafeAreaView`; the absolute blurred tab bar needs bottom safe-area padding
- **Single stream on tablet**: even on iPad/large screens keep a single centered column (max ~620pt) — Vero is anti-grid, pro-reading
- **Chronological guarantee**: render the feed from a strictly time-sorted source; new items prepend with a `FadeIn` — never re-sort or insert "suggested" items
- **Gradient discipline**: only the wordmark, the Post CTA, the compose ring, and active states use `brandGradient`; everything else is flat teal `#00C2B8` or grayscale
- **Accessibility**: the 7 post-type selector should be one `accessibilityRole="radiogroup"` with each cell `accessibilityState={{ selected }}`; set `allowFontScaling={false}` on type labels, tab labels, and star ratings
- **Media**: respect the photographer's original aspect ratio — never force-crop; default portrait is 4:5
- **Haptics**: keep them soft and sparse — Vero's whole ethos is calm; avoid heavy/notification haptics
