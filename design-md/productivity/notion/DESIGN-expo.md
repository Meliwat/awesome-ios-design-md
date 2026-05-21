# Notion (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Notion's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceGray:    '#F7F6F3',
  surfacePressed: '#EFEEE9',
  divider:        '#EAECEC',

  // Surfaces (dark)
  darkCanvas:     '#191919',
  darkSurface1:   '#202020',
  darkSurface2:   '#2F2F2F',
  darkDivider:    '#373737',

  // Text
  textPrimary:    '#37352F',
  textSecondary:  '#9B9A97',
  textTertiary:   '#C3C2BF',
  darkTextPrimary:'#E6E6E4',

  // Brand
  notionBlack:    '#000000',
  linkBlue:       '#2E75CC',
  darkLink:       '#529CCA',

  // Pastel page backgrounds (light)
  bgGray:   '#F1F1EF',
  bgBrown:  '#F4EEEE',
  bgOrange: '#FAEBDD',
  bgYellow: '#FBF3DB',
  bgGreen:  '#EDF3EC',
  bgBlue:   '#E7F3F8',
  bgPurple: '#F6F3F9',
  bgPink:   '#FAF1F5',
  bgRed:    '#FDEBEC',

  // Callout text colors
  textBrown:  '#64473A',
  textOrange: '#D9730D',
  textYellow: '#DFAB01',
  textGreen:  '#0F7B6C',
  textBlue:   '#0B6E99',
  textPurple: '#6940A5',
  textPink:   '#AD1A72',
  textRed:    '#E03E3E',
} as const;

export type NotionColor = keyof typeof colors;

// Pair pastel bg with matching text color
export const calloutPairs = {
  gray:   { bg: colors.bgGray,   text: colors.textSecondary },
  brown:  { bg: colors.bgBrown,  text: colors.textBrown },
  orange: { bg: colors.bgOrange, text: colors.textOrange },
  yellow: { bg: colors.bgYellow, text: colors.textYellow },
  green:  { bg: colors.bgGreen,  text: colors.textGreen },
  blue:   { bg: colors.bgBlue,   text: colors.textBlue },
  purple: { bg: colors.bgPurple, text: colors.textPurple },
  pink:   { bg: colors.bgPink,   text: colors.textPink },
  red:    { bg: colors.bgRed,    text: colors.textRed },
} as const;
```

## 2. Typography

Load Inter, Lora, IBM Plex Mono via `expo-font`. User-switchable via app setting.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-Semibold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
    'Lora-Regular':   require('../assets/fonts/Lora-Regular.ttf'),
    'Lora-Italic':    require('../assets/fonts/Lora-Italic.ttf'),
    'Lora-Bold':      require('../assets/fonts/Lora-Bold.ttf'),
    'IBMPlexMono-Regular': require('../assets/fonts/IBMPlexMono-Regular.ttf'),
    'IBMPlexMono-Medium':  require('../assets/fonts/IBMPlexMono-Medium.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#37352F' } satisfies TextStyle;

export const typography = {
  pageTitle:  { ...primary, fontFamily: 'Inter-Bold',    fontSize: 32, lineHeight: 38, letterSpacing: -0.5 },
  h1:         { ...primary, fontFamily: 'Inter-Bold',    fontSize: 24, lineHeight: 30, letterSpacing: -0.3 },
  h2:         { ...primary, fontFamily: 'Inter-Bold',    fontSize: 20, lineHeight: 26, letterSpacing: -0.2 },
  h3:         { ...primary, fontFamily: 'Inter-Bold',    fontSize: 16, lineHeight: 22, letterSpacing: -0.1 },
  body:       { ...primary, fontFamily: 'Inter-Regular', fontSize: 16, lineHeight: 24 },
  bodyDense:  { ...primary, fontFamily: 'Inter-Regular', fontSize: 15, lineHeight: 21 },
  caption:    { ...primary, fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 19 },
  button:     { ...primary, fontFamily: 'Inter-Semibold', fontSize: 14, lineHeight: 14 },
  mention:    { color: '#2E75CC', fontFamily: 'Inter-Medium', fontSize: 16, lineHeight: 24 },
  link:       { color: '#2E75CC', fontFamily: 'Inter-Regular', fontSize: 16, lineHeight: 24, textDecorationLine: 'underline' as const },
  placeholder:{ color: '#9B9A97', fontFamily: 'Inter-Regular', fontSize: 16, lineHeight: 24 },
  quote:      { ...primary, fontFamily: 'Lora-Italic', fontSize: 17, lineHeight: 26 },
  codeInline: { ...primary, fontFamily: 'IBMPlexMono-Regular', fontSize: 14, lineHeight: 20 },
  codeBlock:  { ...primary, fontFamily: 'IBMPlexMono-Regular', fontSize: 13, lineHeight: 20 },
  tab:        { color: '#9B9A97', fontFamily: 'Inter-Medium', fontSize: 11, lineHeight: 11, letterSpacing: 0.1 },
  sidebar:    { ...primary, fontFamily: 'Inter-Medium', fontSize: 14, lineHeight: 18 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Page Header (Cover + Emoji + Title)

```tsx
// components/PageHeader.tsx
import { Image, TextInput, View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PageHeader({
  coverUri, emoji, titleValue, onChangeTitle,
}: {
  coverUri?: string;
  emoji?: string;
  titleValue: string;
  onChangeTitle: (t: string) => void;
}) {
  return (
    <View>
      {coverUri ? (
        <Image source={{ uri: coverUri }} style={{ width: '100%', height: 120 }} resizeMode="cover" />
      ) : null}
      {emoji ? (
        <View style={{ paddingLeft: 16, marginTop: coverUri ? -32 : 0 }}>
          <Text style={{ fontSize: 64 }}>{emoji}</Text>
        </View>
      ) : null}
      <TextInput
        value={titleValue}
        onChangeText={onChangeTitle}
        placeholder="Untitled"
        placeholderTextColor={colors.textSecondary}
        style={[typography.pageTitle, { paddingHorizontal: 16, paddingTop: 8, paddingBottom: 16 }]}
      />
    </View>
  );
}
```

### Paragraph Block with Handles

```tsx
// components/ParagraphBlock.tsx
import { useState } from 'react';
import { Pressable, TextInput, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ParagraphBlock({
  initialValue = '',
  onOpenMenu,
  onInsertBelow,
}: {
  initialValue?: string;
  onOpenMenu: () => void;
  onInsertBelow: () => void;
}) {
  const [text, setText] = useState(initialValue);
  const handlesOpacity = useSharedValue(0);
  const animStyle = useAnimatedStyle(() => ({ opacity: handlesOpacity.value }));

  const onLongPress = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
    handlesOpacity.value = withTiming(1, { duration: 120 });
  };

  return (
    <Pressable
      onLongPress={onLongPress}
      delayLongPress={400}
      style={{ flexDirection: 'row', alignItems: 'flex-start', paddingHorizontal: 16, paddingVertical: 3, gap: 4 }}
    >
      <Animated.View style={[{ flexDirection: 'row', width: 44, justifyContent: 'flex-end', gap: 2 }, animStyle]}>
        <Pressable onPress={onInsertBelow} hitSlop={8} style={{ width: 20, height: 20, alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name="add" size={14} color={colors.textSecondary} />
        </Pressable>
        <Pressable onPress={onOpenMenu} hitSlop={8} style={{ width: 20, height: 20, alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name="ellipsis-vertical" size={14} color={colors.textSecondary} />
        </Pressable>
      </Animated.View>

      <View style={{ flex: 1 }}>
        <TextInput
          value={text}
          onChangeText={setText}
          placeholder="Type '/' for commands"
          placeholderTextColor={colors.textSecondary}
          multiline
          style={typography.body}
        />
      </View>
    </Pressable>
  );
}
```

### Callout Block

```tsx
// components/CalloutBlock.tsx
import { Text, View } from 'react-native';
import { typography } from '../theme/typography';
import { calloutPairs } from '../theme/colors';

export function CalloutBlock({
  emoji,
  color = 'yellow',
  children,
}: {
  emoji: string;
  color?: keyof typeof calloutPairs;
  children: React.ReactNode;
}) {
  const pair = calloutPairs[color];
  return (
    <View style={{
      flexDirection: 'row',
      alignItems: 'flex-start',
      gap: 10,
      padding: 16,
      marginHorizontal: 16,
      backgroundColor: pair.bg,
      borderRadius: 4,
    }}>
      <Text style={{ fontSize: 20 }}>{emoji}</Text>
      <Text style={[typography.body, { color: pair.text, flex: 1 }]}>{children}</Text>
    </View>
  );
}
```

### Toggle Block

```tsx
// components/ToggleBlock.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Animated, { FadeIn, FadeOut, useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ToggleBlock({ title, children }: { title: string; children: React.ReactNode }) {
  const [isOpen, setIsOpen] = useState(false);
  const rotation = useSharedValue(0);
  const chevronStyle = useAnimatedStyle(() => ({ transform: [{ rotate: `${rotation.value}deg` }] }));

  return (
    <View>
      <Pressable
        onPress={() => {
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
          const next = !isOpen;
          setIsOpen(next);
          rotation.value = withTiming(next ? 90 : 0, { duration: 150 });
        }}
        style={{ flexDirection: 'row', alignItems: 'center', gap: 4, paddingHorizontal: 16, paddingVertical: 6 }}
      >
        <Animated.View style={chevronStyle}>
          <Ionicons name="chevron-forward" size={14} color={colors.textSecondary} />
        </Animated.View>
        <Text style={[typography.body, { fontFamily: 'Inter-Medium' }]}>{title}</Text>
      </Pressable>
      {isOpen ? (
        <Animated.View entering={FadeIn.duration(200)} exiting={FadeOut.duration(150)} style={{ paddingLeft: 24 }}>
          {children}
        </Animated.View>
      ) : null}
    </View>
  );
}
```

### Code Block

```tsx
// components/CodeBlock.tsx
import { Pressable, ScrollView, Text, View } from 'react-native';
import * as Clipboard from 'expo-clipboard';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CodeBlock({ language, code }: { language: string; code: string }) {
  return (
    <View style={{ marginHorizontal: 16, backgroundColor: colors.surfaceGray, borderRadius: 4 }}>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center', paddingHorizontal: 12, paddingTop: 8 }}>
        <Text style={{ fontFamily: 'IBMPlexMono-Regular', fontSize: 12, color: colors.textSecondary }}>{language}</Text>
        <Pressable onPress={() => Clipboard.setStringAsync(code)} hitSlop={8}>
          <Ionicons name="copy-outline" size={14} color={colors.textSecondary} />
        </Pressable>
      </View>
      <ScrollView horizontal showsHorizontalScrollIndicator={false}>
        <Text style={[typography.codeBlock, { padding: 16 }]}>{code}</Text>
      </ScrollView>
    </View>
  );
}
```

### Mention Chip

```tsx
// components/MentionChip.tsx
import { Image, Text, View } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function MentionChip({ avatarUri, name }: { avatarUri?: string; name: string }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 4,
      paddingVertical: 2, paddingHorizontal: 4,
      backgroundColor: `${colors.linkBlue}1A`,
      borderRadius: 2,
    }}>
      {avatarUri ? (
        <Image source={{ uri: avatarUri }} style={{ width: 16, height: 16, borderRadius: 8 }} />
      ) : (
        <View style={{ width: 16, height: 16, borderRadius: 8, backgroundColor: `${colors.linkBlue}33` }} />
      )}
      <Text style={typography.mention}>@{name}</Text>
    </View>
  );
}
```

### `/` Command Palette

```tsx
// components/SlashCommandPalette.tsx
import { FlatList, Text, View, Pressable } from 'react-native';
import Animated, { FadeIn, FadeOut, SlideInDown } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Option = { id: string; icon: any; title: string; subtitle: string };

export function SlashCommandPalette({ options, onPick }: { options: Option[]; onPick: (id: string) => void }) {
  return (
    <Animated.View
      entering={FadeIn.duration(150)}
      exiting={FadeOut.duration(150)}
      style={{
        width: 280,
        backgroundColor: colors.canvas,
        borderRadius: 6,
        borderWidth: 1, borderColor: colors.divider,
        shadowColor: '#000', shadowOpacity: 0.08, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
        elevation: 4,
      }}
    >
      <FlatList
        data={options}
        keyExtractor={(o) => o.id}
        renderItem={({ item }) => (
          <Pressable
            onPress={() => onPick(item.id)}
            style={({ pressed }) => ({
              flexDirection: 'row', alignItems: 'center', gap: 12,
              paddingHorizontal: 12, paddingVertical: 8, height: 40,
              backgroundColor: pressed ? colors.surfacePressed : 'transparent',
            })}
          >
            <Ionicons name={item.icon} size={18} color={colors.textPrimary} />
            <View style={{ flex: 1 }}>
              <Text style={[typography.caption, { fontFamily: 'Inter-Medium' }]}>{item.title}</Text>
              <Text style={{ fontFamily: 'Inter-Regular', fontSize: 12, color: colors.textSecondary }}>{item.subtitle}</Text>
            </View>
          </Pressable>
        )}
      />
    </Animated.View>
  );
}
```

### Sidebar (Slide-in from left)

```tsx
// components/Sidebar.tsx
import { Text, View, Pressable, ScrollView } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Page = { id: string; icon: string; title: string; children?: Page[] };

export function Sidebar({ sections }: { sections: { title: string; pages: Page[] }[] }) {
  return (
    <View style={{ width: 280, backgroundColor: colors.surfaceGray, flex: 1 }}>
      <ScrollView contentContainerStyle={{ paddingTop: 12 }}>
        {sections.map((s) => (
          <View key={s.title} style={{ marginBottom: 16 }}>
            <Text style={[typography.tab, { paddingHorizontal: 12, paddingTop: 8, textTransform: 'uppercase' }]}>{s.title}</Text>
            {s.pages.map((p) => <PageRow key={p.id} page={p} indent={0} />)}
          </View>
        ))}
      </ScrollView>
    </View>
  );
}

function PageRow({ page, indent }: { page: Page; indent: number }) {
  return (
    <Pressable style={({ pressed }) => ({
      flexDirection: 'row', alignItems: 'center', gap: 8,
      paddingLeft: 12 + indent * 24, paddingRight: 12,
      height: 32,
      backgroundColor: pressed ? colors.surfacePressed : 'transparent',
    })}>
      <Ionicons name="chevron-forward" size={10} color={colors.textSecondary} />
      <Text style={{ fontSize: 16 }}>{page.icon}</Text>
      <Text style={typography.sidebar}>{page.title}</Text>
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
        tabBarActiveTintColor:  colors.textPrimary,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: {
          backgroundColor: colors.canvas,
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home-outline"    size={24} color={color} /> }} />
      <Tabs.Screen name="search"    options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search"          size={24} color={color} /> }} />
      <Tabs.Screen name="updates"   options={{ title: 'Updates',  tabBarIcon: ({ color }) => <Ionicons name="notifications-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="settings"  options={{ title: 'Settings', tabBarIcon: ({ color }) => <Ionicons name="settings-outline" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Toggle expand/collapse — Reanimated layout animation
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';

// entering={FadeIn.duration(200)} exiting={FadeOut.duration(150)}

// Block handle reveal (long-press)
// delayLongPress={400} + Animated opacity withTiming(1, { duration: 120 })

// / command palette
// entering={FadeIn.duration(150)} exiting={FadeOut.duration(150)}

// Sidebar slide
// Use expo-router Drawer navigator OR a custom Animated.View with DragGesture (react-native-gesture-handler)
// Open spring: withSpring(1, { damping: 14, stiffness: 120 })

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);  // toggle, block drag start, handle reveal
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Notion has rich block-type iconography — for custom icons use `expo/vector-icons/MaterialCommunityIcons` or bundle SVG via `expo-image`.

| Purpose | Ionicons |
|---------|----------|
| Home | `home-outline` |
| Search | `search` |
| Updates | `notifications-outline` |
| Settings | `settings-outline` |
| Hamburger | `menu` |
| Back | `chevron-back` |
| More | `ellipsis-vertical` |
| Insert | `add` |
| Toggle closed | `chevron-forward` |
| Toggle open | `chevron-down` |
| Copy | `copy-outline` |
| Share | `share-outline` |
| Checkbox (empty) | `square-outline` |
| Checkbox (checked) | `checkbox` |
| Link | `link` |
| Page | `document-text-outline` |
| Database — Table | `grid` |
| Database — Board | `albums-outline` |
| Database — Calendar | `calendar-outline` |
| Database — Gallery | `apps-outline` |
| Code | `code-slash` |
| Image | `image-outline` |
| Emoji picker | `happy-outline` |

## 7. Platform Notes

- **Font choice**: Inter / Lora / IBM Plex Mono are all SIL OFL licensed — free to bundle. Ship all three and let user pick in settings
- **User typography switching**: wrap app in a `UserPreferenceContext` providing the current font family; re-render text components when it changes
- **Status bar**: `<StatusBar style="dark" />` on light mode, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; sidebar and bottom tab bar need safe-area padding
- **Dynamic Type**: React Native respects system scale on `<Text>`; set `allowFontScaling={false}` on tab labels, sidebar labels, mention chips, command palette option text
- **Keyboard**: use `KeyboardAvoidingView` on the page editor so the command palette appears above the keyboard
- **Dark mode**: use `useColorScheme()` to swap color tokens to `darkCanvas` / `darkSurface1` / `darkLink`
- **Accessibility**: every block should be reachable as an accessible element; long-press gesture should have VoiceOver equivalent via `.accessibilityActions`
- **Drag-to-reorder blocks**: use `react-native-draggable-flatlist` or the newer `reanimated`-based `FlatList` reorder; lift-to-drag haptic on start
- **Command palette keyboard navigation**: on external keyboard (iPad with keyboard), listen for arrow key events via `onKeyPress`
