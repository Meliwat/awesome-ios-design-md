# Kindle (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Kindle's visual language into paste-ready Expo / React Native code: a design-token module, a reading-theme model, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand & interactive
  orange:        '#FF9900',
  orangePressed: '#E88B00',
  black:         '#1A1A1A',
  link:          '#1A98FF',
  kindleBlue:    '#4FB3D9',

  // App chrome (light)
  chromeCanvas:  '#FFFFFF',
  surfaceSubtle: '#F4F2EE',
  divider:       '#E4E2DD',

  // App chrome (dark)
  darkCanvas:    '#0E0E0E',
  darkSurface1:  '#1A1A1A',
  darkSurface2:  '#242424',
  darkDivider:   '#2E2E2E',

  // Text (chrome)
  textPrimary:    '#1A1A1A',
  textSecondary:  '#6B6B6B',
  darkTextPrimary:'#E8E8E8',
  darkTextSecondary:'#9A9A9A',

  // Semantic
  error:   '#E0533D',
  success: '#2FAE5F',
} as const;

// Reading themes — the user's choice, OS-independent
export const readingThemes = {
  white: { page: '#FFFFFF', ink: '#1A1A1A' },
  sepia: { page: '#FBF0D9', ink: '#5F4B32' }, // default
  green: { page: '#C5E1C5', ink: '#33492F' },
  dark:  { page: '#2A2A2A', ink: '#D8D8D8' },
  black: { page: '#000000', ink: '#C8C8C8' }, // dimmed, not pure white
} as const;

export type ReadingThemeKey = keyof typeof readingThemes;
export type KindleColor = keyof typeof colors;
```

## 2. Typography

Kindle uses a reading serif (**Bookerly**; ship **Bitter** as a free SIL OFL analog) for book text and **Amazon Ember** for chrome (fall back to system). The reading face/size/spacing/margins are user controls.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Bitter-Regular': require('../assets/fonts/Bitter-Regular.ttf'),
    'Bitter-Bold':    require('../assets/fonts/Bitter-Bold.ttf'),
    'AmazonEmber-Regular': require('../assets/fonts/AmazonEmber-Regular.ttf'),
    'AmazonEmber-Medium':  require('../assets/fonts/AmazonEmber-Medium.ttf'),
    'AmazonEmber-Bold':    require('../assets/fonts/AmazonEmber-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  screenTitle: { fontFamily: 'AmazonEmber-Bold',   fontSize: 30, lineHeight: 36, letterSpacing: -0.4, color: '#1A1A1A' },
  chapterTitle:{ fontFamily: 'Bitter-Bold',         fontSize: 24, lineHeight: 30 },
  bookTitle:   { fontFamily: 'AmazonEmber-Bold',   fontSize: 20, lineHeight: 26, color: '#1A1A1A' },
  subtitle:    { fontFamily: 'AmazonEmber-Medium', fontSize: 17, lineHeight: 24, color: '#1A1A1A' },
  listTitle:   { fontFamily: 'AmazonEmber-Medium', fontSize: 15, lineHeight: 21, color: '#1A1A1A' },
  meta:        { fontFamily: 'AmazonEmber-Regular',fontSize: 14, lineHeight: 19, color: '#6B6B6B' },
  caption:     { fontFamily: 'AmazonEmber-Medium', fontSize: 12, lineHeight: 16, color: '#6B6B6B' },
  eyebrow:     { fontFamily: 'AmazonEmber-Bold',   fontSize: 11, lineHeight: 13, letterSpacing: 1.5 },
  tab:         { fontFamily: 'AmazonEmber-Medium', fontSize: 10, lineHeight: 12, letterSpacing: 0.1 },
  note:        { fontFamily: 'AmazonEmber-Regular',fontSize: 13, lineHeight: 20, color: '#1A1A1A' },
} satisfies Record<string, TextStyle>;

// User reading settings (bind to a persisted store / context)
export type ReadingSettings = {
  fontFamily: string;       // 'Bitter-Regular'
  size: number;             // 15.5
  lineHeightMultiple: number; // 1.72
  margin: number;           // 16 | 26 | 40
  theme: ReadingThemeKey;   // 'sepia'
};
export const defaultReading: ReadingSettings = {
  fontFamily: 'Bitter-Regular', size: 15.5, lineHeightMultiple: 1.72, margin: 26, theme: 'sepia',
};
```

## 3. Signature Components

### Reading Page

```tsx
// components/ReadingPage.tsx
import { Pressable, ScrollView, Text, View } from 'react-native';
import { readingThemes, ReadingSettings } from '../theme/colors';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function ReadingPage({
  chapter, title, paragraphs, percent, minsLeft, settings, onToggleChrome,
}: {
  chapter: string; title: string; paragraphs: string[];
  percent: number; minsLeft: number; settings: ReadingSettings;
  onToggleChrome: () => void;
}) {
  const t = readingThemes[settings.theme];
  return (
    <Pressable style={{ flex: 1, backgroundColor: t.page }} onPress={onToggleChrome}>
      <ScrollView contentContainerStyle={{ paddingHorizontal: settings.margin, paddingVertical: 20 }}
        showsVerticalScrollIndicator={false}>
        <Text style={[typography.eyebrow, { color: t.ink, opacity: 0.55, marginBottom: 4 }]}>{chapter}</Text>
        <Text style={[typography.chapterTitle, { color: t.ink, marginBottom: 6 }]}>{title}</Text>
        {paragraphs.map((p, i) => (
          <Text key={i} style={{
            fontFamily: settings.fontFamily,
            fontSize: settings.size,
            lineHeight: settings.size * settings.lineHeightMultiple,
            color: t.ink,
            marginBottom: 16,
            textAlign: 'justify',   // RN supports justify; pair with hyphenation below
          }}>{p}</Text>
        ))}
      </ScrollView>
      {/* footer whisper + orange progress hairline */}
      <View style={{ paddingHorizontal: 24, paddingBottom: 14 }}>
        <View style={{ height: 2, backgroundColor: `${t.ink}2E`, borderRadius: 1, marginBottom: 8 }}>
          <View style={{ position: 'absolute', left: 0, top: 0, bottom: 0, width: `${percent}%`, backgroundColor: colors.orange }} />
        </View>
        <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
          <Text style={[typography.eyebrow, { color: t.ink, opacity: 0.55 }]}>{percent}%</Text>
          <Text style={[typography.eyebrow, { color: t.ink, opacity: 0.55 }]}>{minsLeft} min left in chapter</Text>
        </View>
      </View>
    </Pressable>
  );
}
// NOTE: RN `textAlign:'justify'` works on iOS; for hyphenated justification matching
// Kindle, pre-insert soft hyphens (­) or render via a WebView-backed paginator.
```

### Aa Typography Panel

```tsx
// components/AaPanel.tsx
import { Pressable, Text, View } from 'react-native';
import Slider from '@react-native-community/slider';
import * as Haptics from 'expo-haptics';
import { readingThemes, ReadingSettings, ReadingThemeKey, colors } from '../theme/colors';

const THEMES = Object.keys(readingThemes) as ReadingThemeKey[];

export function AaPanel({ settings, onChange }: {
  settings: ReadingSettings; onChange: (s: ReadingSettings) => void;
}) {
  const t = readingThemes[settings.theme];
  return (
    <View style={{ padding: 20, borderTopLeftRadius: 12, borderTopRightRadius: 12, backgroundColor: t.page }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, marginBottom: 18 }}>
        <Text style={{ fontFamily: 'Bitter-Bold', fontSize: 14, color: t.ink }}>Aa</Text>
        <Slider style={{ flex: 1 }} minimumValue={12} maximumValue={26} step={0.5}
          value={settings.size} minimumTrackTintColor={colors.orange}
          onValueChange={(v) => onChange({ ...settings, size: v })} />
        <Text style={{ fontFamily: 'Bitter-Bold', fontSize: 24, color: t.ink }}>Aa</Text>
      </View>
      <View style={{ flexDirection: 'row', gap: 12 }}>
        {THEMES.map((k) => {
          const active = settings.theme === k;
          return (
            <Pressable key={k} onPress={() => {
              onChange({ ...settings, theme: k });
              Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
            }}>
              <View style={{
                width: 30, height: 30, borderRadius: 15,
                backgroundColor: readingThemes[k].page,
                borderWidth: 1, borderColor: 'rgba(0,0,0,0.18)',
              }} />
              {active && (
                <View style={{
                  position: 'absolute', top: -4, left: -4, right: -4, bottom: -4,
                  borderRadius: 19, borderWidth: 2, borderColor: colors.orange,
                }} />
              )}
            </Pressable>
          );
        })}
      </View>
    </View>
  );
}
```

### Library Cover Cell

```tsx
// components/LibraryCover.tsx
import { Image, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LibraryCover({ uri, progress, author }: {
  uri?: string; progress: number; author: string;
}) {
  return (
    <View style={{ gap: 6 }}>
      <View style={{ aspectRatio: 2 / 3, borderRadius: 4, overflow: 'hidden',
        shadowColor: '#000', shadowOpacity: 0.4, shadowRadius: 10, shadowOffset: { width: 0, height: 4 }, elevation: 6 }}>
        <Image source={{ uri }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
        <View style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: 3, backgroundColor: 'rgba(255,255,255,0.2)' }}>
          <View style={{ position: 'absolute', left: 0, bottom: 0, top: 0, width: `${progress * 100}%`, backgroundColor: colors.orange }} />
        </View>
      </View>
      <Text style={typography.caption}>
        {progress >= 1 ? `Finished · ${author}` : `${Math.round(progress * 100)}% · ${author}`}
      </Text>
    </View>
  );
}
```

### Reading Progress Bar

```tsx
// components/KindleProgress.tsx
import { Text, View, useColorScheme } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function KindleProgress({ fraction, caption }: { fraction: number; caption: string }) {
  const dark = useColorScheme() === 'dark';
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12 }}>
      <View style={{ flex: 1, height: 4, borderRadius: 2, overflow: 'hidden',
        backgroundColor: dark ? colors.darkSurface2 : colors.divider }}>
        <View style={{ height: '100%', width: `${fraction * 100}%`, backgroundColor: colors.orange }} />
      </View>
      <Text style={[typography.caption, { width: 96 }]}>{caption}</Text>
    </View>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useColorScheme } from 'react-native';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  const dark = useColorScheme() === 'dark';
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.orange,
        tabBarInactiveTintColor: dark ? colors.darkTextSecondary : colors.textSecondary,
        tabBarStyle: {
          backgroundColor: dark ? colors.darkCanvas : colors.chromeCanvas,
          borderTopWidth: 0.5,
          borderTopColor: dark ? colors.darkDivider : colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'AmazonEmber-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home"    size={22} color={color} /> }} />
      <Tabs.Screen name="library"  options={{ title: 'Library',  tabBarIcon: ({ color }) => <Ionicons name="library" size={22} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="search"  size={22} color={color} /> }} />
      <Tabs.Screen name="more"     options={{ title: 'More',     tabBarIcon: ({ color }) => <Ionicons name="ellipsis-horizontal" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Chrome fade on book open / center-tap
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';
// chrome: entering={FadeIn.duration(200)} exiting={FadeOut.duration(250)}

// Theme swatch select — ring pop + page cross-fade + soft haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
// ring: useSharedValue scale withSpring(1.15) then withSpring(1)
// page bg: animate via withTiming on a derived color (Reanimated interpolateColor)

// Library progress fill on appear
// width animated via withTiming(progress, { duration: 500 })

// Continue-reading cover scale-in
// scale: withTiming(1, { duration: 250 }) from 0.96

// Highlight commit — fill range + soft haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Page turn — slide (Reanimated) or a paginated FlatList with horizontal paging
// curl effect: use a WebView-backed paginator or react-native-page-curl if needed
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Library | `library` |
| Discover / Search | `search` |
| More | `ellipsis-horizontal` |
| Back to library | `chevron-back` |
| Aa panel | (text "Aa") |
| Brightness | `sunny-outline` |
| Bookmark | `bookmark-outline` / `bookmark` |
| Search in book | `search` |
| Contents / Go To | `list` |
| X-Ray | `people-outline` |
| Sync furthest | `sync-outline` |
| Downloaded | `checkmark-circle` |
| Not downloaded | `cloud-download-outline` |
| Highlight | `color-wand-outline` |
| Note | `create-outline` |
| Dictionary / Lookup | `book-outline` |
| Translate | `language-outline` |
| Share | `share-outline` |

## 7. Platform Notes

- **Fonts**: ship **Bitter** (SIL OFL — free) as the Bookerly-analog reading face; Amazon Ember requires Amazon's license, so for non-Amazon builds fall chrome back to system (`-apple-system`)
- **Justified reading body**: RN `textAlign:'justify'` works on iOS but does not hyphenate — pre-insert soft hyphens (`­`) into the text or render the page through a WebView-backed paginator for true Kindle-grade justification + pagination
- **Two surface contexts**: the reading view honors `settings.theme` (OS-independent); app chrome uses `useColorScheme()`. Do NOT couple them
- **Black theme ink**: use `#C8C8C8`, never white — reduces halation
- **Status bar**: in the reader, set `<StatusBar>` style to match the active theme (dark content on White/Sepia/Green, light on Dark/Black) and hide it while chrome is hidden
- **Safe area**: footer whisper sits above the home indicator; chrome respects safe area
- **Reading size**: the in-app size control is primary; honor system font scale only when the user enables "Match system size" (`PixelRatio.getFontScale()`)
- **Dark mode (chrome)**: `useColorScheme()` swaps to `darkCanvas` / `darkSurface1`; orange `#FF9900` stays constant in all themes
- **Cover shadows on Android**: also set `elevation` (shown) since iOS `shadow*` props are ignored on Android
- **Lists**: use `FlashList` for the library grid and in-book search results
- **Accessibility**: label covers "{title} by {author}, {percent}% read"; theme swatches "Sepia theme, selected"; provide explicit Next/Previous page buttons in addition to tap zones; ship an OpenDyslexic reading face option
