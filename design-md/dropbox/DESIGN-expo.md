# Dropbox (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Dropbox's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#FFFFFF',
  surface:  '#F7F5F2',
  divider:  '#E6E1DA',

  textPrimary:   '#1E1919',
  textSecondary: '#6F6A65',
  textTertiary:  '#A39E98',

  blue:        '#0061FF',
  bluePressed: '#0050D0',
  blueTint:    '#E6F0FF',

  darkCanvas:  '#1E1919',
  darkSurface: '#2A2424',
  darkDivider: '#3A3331',
  darkBlue:    '#3D8BFF',

  pdfRed:     '#FA551E',
  docBlue:    '#0061FF',
  sheetGreen: '#1A8754',
  imageTeal:  '#00B2A9',
  folderSlate:'#8C97A8',

  success: '#1A8754',
  warning: '#FFAF00',
  error:   '#D1180B',
} as const;

export type DropboxColor = keyof typeof colors;
```

## 2. Typography

Sharp Grotesk is Dropbox's licensed brand typeface. Load it via `expo-font`, or use **Inter** (closest free grotesque) as the substitute.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Sharp-Regular':  require('../assets/fonts/SharpGrotesk-Regular.ttf'),
    'Sharp-Semibold': require('../assets/fonts/SharpGrotesk-Semibold.ttf'),
    'Sharp-Bold':     require('../assets/fonts/SharpGrotesk-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const ink = { color: '#1E1919' } satisfies TextStyle;

export const typography = {
  titleLarge: { ...ink, fontFamily: 'Sharp-Bold',     fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  section:    { ...ink, fontFamily: 'Sharp-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  sheetTitle: { ...ink, fontFamily: 'Sharp-Bold',     fontSize: 20, lineHeight: 24, letterSpacing: -0.2 },
  cardTitle:  { ...ink, fontFamily: 'Sharp-Semibold', fontSize: 17, lineHeight: 22, letterSpacing: -0.1 },
  rowName:    { ...ink, fontFamily: 'Sharp-Semibold', fontSize: 16, lineHeight: 20, letterSpacing: -0.1 },
  body:       { ...ink, fontFamily: 'Sharp-Regular',  fontSize: 15, lineHeight: 22 },
  meta:       {          fontFamily: 'Sharp-Regular',  fontSize: 14, lineHeight: 18, color: '#6F6A65', fontVariant: ['tabular-nums'] as const },
  caption:    {          fontFamily: 'Sharp-Regular',  fontSize: 12, lineHeight: 16, color: '#6F6A65', fontVariant: ['tabular-nums'] as const },
  labelUpper: {          fontFamily: 'Sharp-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.6, color: '#6F6A65', textTransform: 'uppercase' as const },
  button:     { color: '#FFFFFF', fontFamily: 'Sharp-Semibold', fontSize: 16, lineHeight: 20 },
  tab:        {          fontFamily: 'Sharp-Semibold', fontSize: 11, lineHeight: 13, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary Button

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({
  title, enabled = true, onPress,
}: { title: string; enabled?: boolean; onPress: () => void }) {
  return (
    <Pressable
      disabled={!enabled}
      onPress={onPress}
      style={({ pressed }) => ({
        paddingVertical: 14,
        paddingHorizontal: 24,
        borderRadius: 8,
        alignItems: 'center',
        backgroundColor: pressed ? colors.bluePressed : colors.blue,
        opacity: enabled ? 1 : 0.4,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Upload / Create FAB

```tsx
// components/UploadFAB.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function UploadFAB({ onPress }: { onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.92, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
        onPress();
      }}
      style={{ position: 'absolute', right: 16, bottom: 16 }}
    >
      <Animated.View
        style={[
          {
            width: 56, height: 56, borderRadius: 28,
            backgroundColor: colors.blue,
            alignItems: 'center', justifyContent: 'center',
            shadowColor: colors.blue, shadowOpacity: 0.32,
            shadowRadius: 20, shadowOffset: { width: 0, height: 8 },
            elevation: 8,
          },
          style,
        ]}
      >
        <Ionicons name="add" size={24} color="#FFF" />
      </Animated.View>
    </Pressable>
  );
}
```

### File / Folder Row

```tsx
// components/FileRow.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Kind = 'pdf' | 'doc' | 'sheet' | 'image' | 'folder';
const ICON: Record<Kind, { name: any; color: string }> = {
  pdf:    { name: 'document-text', color: colors.pdfRed },
  doc:    { name: 'document-text', color: colors.docBlue },
  sheet:  { name: 'grid',          color: colors.sheetGreen },
  image:  { name: 'image',         color: colors.imageTeal },
  folder: { name: 'folder',        color: colors.folderSlate },
};

export function FileRow({
  name, meta, kind, selected = false, onPress,
}: { name: string; meta: string; kind: Kind; selected?: boolean; onPress: () => void }) {
  const ic = ICON[kind];
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        height: 60, flexDirection: 'row', alignItems: 'center',
        paddingHorizontal: 16, gap: 12,
        backgroundColor: selected ? colors.blueTint : pressed ? colors.surface : colors.canvas,
      })}
    >
      {selected ? (
        <Ionicons name="checkmark-circle" size={40} color={colors.blue} />
      ) : (
        <View style={{
          width: 40, height: 40, borderRadius: 8,
          backgroundColor: ic.color + '24',
          alignItems: 'center', justifyContent: 'center',
        }}>
          <Ionicons name={ic.name} size={20} color={ic.color} />
        </View>
      )}
      <View style={{ flex: 1, gap: 2 }}>
        <Text style={typography.rowName} numberOfLines={1}>{name}</Text>
        <Text style={typography.meta} numberOfLines={1}>{meta}</Text>
      </View>
      <Ionicons
        name={kind === 'folder' ? 'chevron-forward' : 'ellipsis-horizontal'}
        size={18} color={colors.textSecondary}
      />
    </Pressable>
  );
}
```

### Upload Progress Bar

```tsx
// components/UploadBar.tsx
import { Text, View } from 'react-native';
import Animated, { useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function UploadBar({
  label, progress, done = false,
}: { label: string; progress: number; done?: boolean }) {
  const fill = useAnimatedStyle(() => ({
    width: withTiming(`${Math.round(progress * 100)}%`, { duration: 200 }),
  }));
  return (
    <View style={{ paddingHorizontal: 16, paddingVertical: 12, backgroundColor: colors.canvas }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', marginBottom: 6 }}>
        <Text style={[typography.meta, { color: colors.textPrimary, fontWeight: '600', flex: 1 }]}>
          {label}
        </Text>
        {done && <Ionicons name="checkmark-circle" size={18} color={colors.success} />}
      </View>
      <View style={{ height: 3, borderRadius: 2, backgroundColor: colors.divider, overflow: 'hidden' }}>
        <Animated.View style={[{ height: 3, backgroundColor: colors.blue }, fill]} />
      </View>
    </View>
  );
}
```

### Recent File Card

```tsx
// components/RecentCard.tsx
import { Image, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RecentCard({
  name, meta, thumbUri,
}: { name: string; meta: string; thumbUri?: string }) {
  return (
    <View style={{
      width: 140, height: 160, padding: 12,
      borderRadius: 12, borderWidth: 1, borderColor: colors.divider,
      backgroundColor: colors.canvas,
    }}>
      <View style={{
        width: 116, height: 96, borderRadius: 4, marginBottom: 8,
        backgroundColor: colors.surface, alignItems: 'center', justifyContent: 'center',
        overflow: 'hidden',
      }}>
        {thumbUri
          ? <Image source={{ uri: thumbUri }} style={{ width: '100%', height: '100%' }} />
          : <Ionicons name="document" size={28} color={colors.blue} />}
      </View>
      <Text style={typography.cardTitle} numberOfLines={2}>{name}</Text>
      <Text style={typography.caption}>{meta}</Text>
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
        tabBarActiveTintColor:   colors.blue,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: 'rgba(255,255,255,0.94)', borderTopColor: colors.divider, borderTopWidth: 0.5 },
        tabBarLabelStyle: { fontFamily: 'Sharp-Semibold', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home-outline"   size={24} color={color} /> }} />
      <Tabs.Screen name="files"   options={{ title: 'Files',   tabBarIcon: ({ color }) => <Ionicons name="folder-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="photos"  options={{ title: 'Photos',  tabBarIcon: ({ color }) => <Ionicons name="images-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="offline" options={{ title: 'Offline', tabBarIcon: ({ color }) => <Ionicons name="cloud-download-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="account" options={{ title: 'Account', tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Photo Grid (contact-sheet)

```tsx
import { FlatList, Pressable, Image, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function PhotoGrid({ uris }: { uris: string[] }) {
  const [selected, setSelected] = React.useState<Set<number>>(new Set());
  const toggle = (i: number) => setSelected(s => {
    const n = new Set(s); n.has(i) ? n.delete(i) : n.add(i); return n;
  });
  return (
    <FlatList
      data={uris}
      numColumns={3}
      keyExtractor={(_, i) => String(i)}
      renderItem={({ item, index }) => (
        <Pressable onLongPress={() => toggle(index)} style={{ flex: 1 / 3, aspectRatio: 1, margin: 1 }}>
          <Image source={{ uri: item }} style={{ flex: 1 }} />
          {selected.has(index) && (
            <>
              <View style={{ position: 'absolute', inset: 0, borderWidth: 4, borderColor: colors.blue }} />
              <Ionicons name="checkmark-circle" size={20} color={colors.blue}
                style={{ position: 'absolute', top: 6, right: 6, backgroundColor: '#fff', borderRadius: 10 }} />
            </>
          )}
        </Pressable>
      )}
    />
  );
}
```

## 6. Motion

```tsx
// FAB tap — soft impact (see UploadFAB)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Star toggle: scale bounce + success haptic
const s = useSharedValue(1);
const onStar = () => {
  s.value = withSequence(withSpring(1.12), withSpring(1));
  Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
};

// Upload progress: drive `progress` from your upload task's bytesSent/total.
// withTiming(..., { duration: 200 }) keeps the bar honest, not flashy.

// Sheet present: use expo-router modal presentation; scrim is rgba(30,25,25,0.40).
```

## 7. Icon Library

Use `@expo/vector-icons` (ships Ionicons). Map to Dropbox's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Upload / Create FAB | `add` |
| PDF / Doc file | `document-text` |
| Sheet file | `grid` |
| Image file | `image` |
| Folder | `folder` |
| Selected | `checkmark-circle` |
| More | `ellipsis-horizontal` |
| Folder chevron | `chevron-forward` |
| Star | `star-outline` / `star` |
| Share | `share-outline` |
| Download / Offline | `cloud-download-outline` |
| Search | `search` |
| Home | `home-outline` / `home` |
| Files | `folder-outline` / `folder` |
| Photos | `images-outline` |
| Account | `person-circle-outline` |
| Success | `checkmark-circle` |

## 8. Platform Notes

- **Tab bar**: iOS gets the `.regularMaterial` feel with a near-opaque `rgba(255,255,255,0.94)` background; on Android it falls back to a solid surface
- **Status bar**: set `<StatusBar style="dark" />` from `expo-status-bar` for the white canvas; switch to `light` when the dark theme is active
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the FAB sits 16pt above the tab bar inside safe area
- **Tabular figures**: `fontVariant: ['tabular-nums']` on `meta`/`caption` keeps file sizes and dates column-aligned
- **Dynamic Type**: React Native honors font scaling by default; set `allowFontScaling={false}` only on the tab labels and the upload-bar height
- **Accessibility**: add `accessibilityRole="button"` + `accessibilityLabel` on the FAB ("Upload or create"), group file-row name + meta and expose the trailing icon as a separate button
