# Figma (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Figma's precise, productivity-tool aesthetic into paste-ready Expo / React Native code: a design-token module, themed components, file cards, cube avatars, comment pins, and Reanimated spring transitions.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // The Five Figma Cubes (brand accents — never primary CTA)
  cubeRed:     '#F24E1E',
  cubeOrange:  '#FF7262',
  cubePurple:  '#A259FF',
  cubeBlue:    '#1ABCFE',
  cubeGreen:   '#0ACF83',

  // UI action colors
  actionBlue:        '#0D99FF',
  actionBluePressed: '#0769B5',
  blurple:           '#5551FF',
  selectionBlue:     '#5EB6FC',

  // Macaron
  macaronPink:       '#FFC8C8',
  macaronPinkBright: '#FFA6A6',

  // Light surfaces
  canvas:        '#FFFFFF',
  surfaceLight1: '#F5F5F5',
  surfaceLight2: '#E5E5E5',
  dividerLight:  '#E5E5E5',
  hairlineLight: '#EEEEEE',

  // Dark surfaces (Editor's exact tokens)
  darkCanvas:    '#1E1E1E',
  darkSurface1:  '#2C2C2C',
  darkSurface2:  '#383838',
  darkSurface3:  '#444444',
  darkDivider:   '#2C2C2C',

  // Text
  ink:            '#1E1E1E',
  textSecondary:  '#757575',
  textTertiary:   '#B3B3B3',
  textDark:       '#FFFFFF',
  textDarkSec:    '#B3B3B3',

  // Semantic
  success:       '#14AE5C',
  warning:       '#F2AC2A',
  error:         '#E03E1A',
} as const;

export type FigmaColor = keyof typeof colors;

export const FIGMA_CUBES = [colors.cubeRed, colors.cubeOrange, colors.cubePurple, colors.cubeBlue, colors.cubeGreen] as const;

/** Deterministic cube color for a stable user ID. */
export function cubeColor(userId: string): string {
  let hash = 0;
  for (let i = 0; i < userId.length; i++) {
    hash = (hash * 31 + userId.charCodeAt(i)) | 0;
  }
  return FIGMA_CUBES[Math.abs(hash) % FIGMA_CUBES.length];
}
```

## 2. Typography

Inter is the open-source face Rasmus Andersson originally drew for Figma. Bundle via `expo-font` from Google Fonts or local TTFs.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';
import {
  Inter_400Regular, Inter_500Medium, Inter_600SemiBold, Inter_700Bold,
} from '@expo-google-fonts/inter';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  Inter_400Regular,
    'Inter-Medium':   Inter_500Medium,
    'Inter-SemiBold': Inter_600SemiBold,
    'Inter-Bold':     Inter_700Bold,
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const TNUM: TextStyle = { fontVariant: ['tabular-nums'] };
const ink   = { color: '#1E1E1E' };
const sec   = { color: '#757575' };

export const typography = {
  // Titles
  hero:          { ...ink, fontFamily: 'Inter-Bold',     fontSize: 28, letterSpacing: -0.4, lineHeight: 34 },
  pageSection:   { ...ink, fontFamily: 'Inter-Bold',     fontSize: 17, letterSpacing: -0.2, lineHeight: 22 },
  subhead:       { ...ink, fontFamily: 'Inter-SemiBold', fontSize: 15, letterSpacing: -0.1, lineHeight: 20 },

  // File / project
  fileName:      { ...ink, fontFamily: 'Inter-Medium',   fontSize: 14, letterSpacing: -0.1, lineHeight: 18 },

  // Body & comments
  body:          { ...ink, fontFamily: 'Inter-Regular',  fontSize: 14, lineHeight: 21 },
  commentBody:   { ...ink, fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 20 },
  commentAuthor: { ...ink, fontFamily: 'Inter-SemiBold', fontSize: 13, lineHeight: 17 },

  // Metadata
  metadata:      { ...sec, fontFamily: 'Inter-Regular',  fontSize: 12, lineHeight: 17 },
  caption:       { ...ink, fontFamily: 'Inter-Medium',   fontSize: 11, lineHeight: 14 },

  // Mono — hex / dimensions
  mono:          { ...TNUM, ...ink, fontFamily: 'SpaceMono-Regular', fontSize: 12, lineHeight: 16 },

  // Buttons & tabs
  button:        { color: '#FFFFFF', fontFamily: 'Inter-SemiBold', fontSize: 14, lineHeight: 14 },
  tab:           { fontFamily: 'Inter-Medium', fontSize: 11, lineHeight: 13 },
  avatar24:      { color: '#FFFFFF', fontFamily: 'Inter-SemiBold', fontSize: 14, lineHeight: 14 },
  avatar32:      { color: '#FFFFFF', fontFamily: 'Inter-SemiBold', fontSize: 16, lineHeight: 16 },
  avatar40:      { color: '#FFFFFF', fontFamily: 'Inter-SemiBold', fontSize: 18, lineHeight: 18 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Cube Avatar

```tsx
// components/CubeAvatar.tsx
import { View, Text } from 'react-native';
import { cubeColor } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = { userId: string; initial: string; size?: 24 | 32 | 40 };

export function CubeAvatar({ userId, initial, size = 32 }: Props) {
  const fontStyle = size === 24 ? typography.avatar24 : size === 40 ? typography.avatar40 : typography.avatar32;
  return (
    <View style={{
      width: size, height: size, borderRadius: size / 2,
      backgroundColor: cubeColor(userId),
      alignItems: 'center', justifyContent: 'center',
    }}>
      <Text style={fontStyle}>{initial.charAt(0).toUpperCase()}</Text>
    </View>
  );
}
```

### Avatar Stack

```tsx
// components/AvatarStack.tsx
import { View, Text } from 'react-native';
import { CubeAvatar } from './CubeAvatar';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Collaborator = { id: string; initial: string };
type Props = { collaborators: Collaborator[]; maxVisible?: number; size?: 24 | 32 | 40 };

export function AvatarStack({ collaborators, maxVisible = 3, size = 24 }: Props) {
  const visible = collaborators.slice(0, maxVisible);
  const overflow = collaborators.length - maxVisible;
  return (
    <View style={{ flexDirection: 'row' }}>
      {visible.map((c, i) => (
        <View
          key={c.id}
          style={{
            marginLeft: i === 0 ? 0 : -8,
            borderRadius: size / 2,
            borderWidth: 2,
            borderColor: colors.canvas,
          }}
        >
          <CubeAvatar userId={c.id} initial={c.initial} size={size} />
        </View>
      ))}
      {overflow > 0 && (
        <View style={{
          width: size, height: size, borderRadius: size / 2,
          backgroundColor: colors.surfaceLight1,
          alignItems: 'center', justifyContent: 'center',
          marginLeft: -8,
          borderWidth: 2, borderColor: colors.canvas,
        }}>
          <Text style={[typography.caption, { fontSize: size === 24 ? 10 : 11 }]}>+{overflow}</Text>
        </View>
      )}
    </View>
  );
}
```

### File Card (16:10 thumbnail + name + metadata)

```tsx
// components/FileCard.tsx
import { Pressable, View, Text, Image, ImageSourcePropType, useWindowDimensions } from 'react-native';
import { AvatarStack } from './AvatarStack';
import { cubeColor, colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Collaborator = { id: string; initial: string };
type Props = {
  id: string;
  name: string;
  metadata: string;
  thumbnail?: ImageSourcePropType;
  collaborators?: Collaborator[];
  onPress: () => void;
};

export function FileCard({ id, name, metadata, thumbnail, collaborators = [], onPress }: Props) {
  const { width } = useWindowDimensions();
  const cardWidth = (width - 16 * 2 - 8) / 2;        // 2-col, 16pt outer, 8pt gap
  const thumbHeight = cardWidth * (10 / 16);

  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({ width: cardWidth, opacity: pressed ? 0.96 : 1 })}>
      <View style={{
        width: cardWidth, height: thumbHeight, borderRadius: 6, overflow: 'hidden',
        borderWidth: 1, borderColor: colors.dividerLight,
        backgroundColor: colors.surfaceLight1,
      }}>
        {thumbnail ? (
          <Image source={thumbnail} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
        ) : (
          <View style={{
            flex: 1, backgroundColor: cubeColor(id),
            alignItems: 'center', justifyContent: 'center',
          }}>
            <Text style={{ fontFamily: 'Inter-Bold', fontSize: 32, color: '#FFFFFF' }}>
              {name.charAt(0).toUpperCase()}
            </Text>
          </View>
        )}
      </View>

      <View style={{ marginTop: 8 }}>
        <Text style={typography.fileName} numberOfLines={1}>{name}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', marginTop: 2 }}>
          <Text style={typography.metadata} numberOfLines={1} style={{ flex: 1 }}>{metadata}</Text>
          {collaborators.length > 0 && <AvatarStack collaborators={collaborators} maxVisible={3} size={20} />}
        </View>
      </View>
    </Pressable>
  );
}
```

### Primary CTA

```tsx
// components/FigmaPrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FigmaPrimaryButton({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={({ pressed }) => ({
        height: 36, borderRadius: 6, paddingHorizontal: 16,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.actionBluePressed : colors.actionBlue,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{label}</Text>
    </Pressable>
  );
}
```

### Comment Pin

```tsx
// components/CommentPin.tsx
import { Pressable, View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withSequence } from 'react-native-reanimated';
import { useColorScheme } from 'react-native';
import { cubeColor, colors } from '../theme/colors';

type Props = { userId: string; number: number; onPress: () => void };

export function CommentPin({ userId, number, onPress }: Props) {
  const scale = useSharedValue(1);
  const cs = useColorScheme();
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const handlePress = () => {
    scale.value = withSequence(withSpring(1.15, { damping: 8 }), withSpring(1, { damping: 8 }));
    onPress();
  };

  return (
    <Pressable onPress={handlePress} hitSlop={12}>
      <Animated.View
        style={[
          {
            width: 28, height: 28, borderRadius: 14,
            backgroundColor: cubeColor(userId),
            alignItems: 'center', justifyContent: 'center',
            borderWidth: 2, borderColor: cs === 'dark' ? colors.darkCanvas : colors.canvas,
            shadowColor: '#000', shadowOpacity: 0.16, shadowRadius: 4, shadowOffset: { width: 0, height: 2 },
            elevation: 3,
          },
          style,
        ]}
      >
        <Text style={{ fontFamily: 'Inter-Bold', fontSize: 13, color: '#FFFFFF' }}>{number}</Text>
      </Animated.View>
    </Pressable>
  );
}
```

### Comment Composer

```tsx
// components/CommentComposer.tsx
import { View, TextInput, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useState } from 'react';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CommentComposer({ onSend }: { onSend: (text: string) => void }) {
  const [text, setText] = useState('');

  return (
    <View style={{
      flexDirection: 'row', alignItems: 'flex-end', gap: 8,
      paddingHorizontal: 12, paddingVertical: 8, minHeight: 36,
      backgroundColor: colors.surfaceLight1, borderRadius: 18,
    }}>
      <Ionicons name="at" size={14} color={colors.textSecondary} style={{ marginBottom: 6 }} />
      <TextInput
        value={text}
        onChangeText={setText}
        placeholder="Reply"
        placeholderTextColor={colors.textSecondary}
        multiline
        style={[typography.commentBody, { flex: 1, paddingTop: 4, paddingBottom: 4, maxHeight: 84 }]}
      />
      {text.length > 0 && (
        <Pressable
          onPress={() => {
            Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
            onSend(text);
            setText('');
          }}
          style={{ width: 28, height: 28, borderRadius: 14, backgroundColor: colors.actionBlue, alignItems: 'center', justifyContent: 'center', marginBottom: 4 }}
        >
          <Ionicons name="paper-plane" size={13} color="#FFFFFF" />
        </Pressable>
      )}
    </View>
  );
}
```

### Project Row

```tsx
// components/ProjectRow.tsx
import { Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { cubeColor, colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = { id: string; name: string; fileCount: number; initial: string; onPress: () => void };

export function ProjectRow({ id, name, fileCount, initial, onPress }: Props) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({ paddingHorizontal: 16, height: 56, flexDirection: 'row', alignItems: 'center', gap: 12, backgroundColor: pressed ? colors.surfaceLight1 : 'transparent' })}>
      <View style={{ width: 32, height: 32, borderRadius: 6, backgroundColor: cubeColor(id), alignItems: 'center', justifyContent: 'center' }}>
        <Text style={typography.caption}>{initial}</Text>
      </View>
      <View style={{ flex: 1, gap: 2 }}>
        <Text style={typography.subhead}>{name}</Text>
        <Text style={typography.metadata}>{fileCount} files</Text>
      </View>
      <Ionicons name="chevron-forward" size={14} color={colors.textSecondary} />
    </Pressable>
  );
}
```

### Plan Badge

```tsx
// components/PlanBadge.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';

type Tier = 'free' | 'pro' | 'org';
const config: Record<Tier, { bg: string; fg: string; label: string }> = {
  free: { bg: colors.macaronPink, fg: colors.ink, label: 'FREE' },
  pro:  { bg: colors.blurple,     fg: '#FFFFFF',  label: 'PRO' },
  org:  { bg: colors.actionBlue,  fg: '#FFFFFF',  label: 'ORG' },
};

export function PlanBadge({ tier }: { tier: Tier }) {
  const c = config[tier];
  return (
    <View style={{ backgroundColor: c.bg, borderRadius: 4, paddingVertical: 2, paddingHorizontal: 6 }}>
      <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 10, color: c.fg, letterSpacing: 0.4 }}>{c.label}</Text>
    </View>
  );
}
```

## 4. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useColorScheme } from 'react-native';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  const cs = useColorScheme();
  const isDark = cs === 'dark';
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   isDark ? colors.canvas : colors.ink,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: isDark ? colors.darkCanvas : colors.canvas, borderTopWidth: 1, borderTopColor: isDark ? colors.darkDivider : colors.dividerLight },
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 11 },
      }}
    >
      <Tabs.Screen name="index"         options={{ title: 'Recents',       tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'time' : 'time-outline'}                   size={22} color={color} /> }} />
      <Tabs.Screen name="drafts"        options={{ title: 'Drafts',        tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'document' : 'document-outline'}           size={22} color={color} /> }} />
      <Tabs.Screen name="notifications" options={{ title: 'Notifications', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'notifications' : 'notifications-outline'} size={22} color={color} /> }} />
      <Tabs.Screen name="profile"       options={{ title: 'Profile',       tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'person' : 'person-outline'}               size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// File card tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Comment pin tap — Reanimated spring
scale.value = withSequence(withSpring(1.15, { damping: 8 }), withSpring(1, { damping: 8 }));

// Send reply
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Tab switch
Haptics.selectionAsync();

// File card → detail
// router.push({ pathname: '/file/[id]', params: { id, animation: 'slide' } });
```

## 6. Icon Library

`@expo/vector-icons` — Ionicons. Mapping:

| Purpose | Ionicons |
|---------|----------|
| Recents tab | `time` / `time-outline` |
| Drafts tab | `document` / `document-outline` |
| Notifications tab | `notifications` / `notifications-outline` |
| Profile tab | `person` / `person-outline` |
| Comment | `chatbubble-outline` |
| Mirror | `phone-portrait-outline` |
| Share | `share-outline` |
| Mention | `at` |
| Send | `paper-plane` |
| Search | `search` |
| Row chevron | `chevron-forward` |
| Plus | `add` |
| Filter | `options-outline` |

## 7. Platform Notes

- **Dark mode is first-class**: `useColorScheme()` swaps every token between light and dark. The dark canvas `#1E1E1E` matches the Figma Editor exactly — this is intentional.
- **Status bar**: `<StatusBar style="auto" />` from `expo-status-bar`.
- **Safe area**: `SafeAreaView` from `react-native-safe-area-context`. The editor bottom pill sits 16pt above the home indicator.
- **Cube persistence**: derive `cubeColor(userId)` from a stable user ID. Store the resolved color or just the user ID — never assign randomly per session.
- **Inter font**: install via `@expo-google-fonts/inter` — supports `Inter_400Regular` through `Inter_900Black` plus italics.
- **OpenType features**: React Native doesn't expose `font-feature-settings` for arbitrary OpenType features (slashed zero, alternate one). For hex codes and pixel dimensions, render in SF Mono via `fontFamily: 'Menlo'` or bundle `SpaceMono-Regular`. Tabular numerals work via `fontVariant: ['tabular-nums']` on iOS.
- **Reanimated**: required for the comment-pin scale spring and the cursor trailing animation. v3+.
- **Tabular numerals**: apply `fontVariant: ['tabular-nums']` on every numeric value — file counts, timestamps, dimensions.
