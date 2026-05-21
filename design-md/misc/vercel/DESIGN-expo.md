# Vercel (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Vercel's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — default & brand)
  canvas:    '#000000', // TRUE black — never #191919
  surface1:  '#0A0A0A',
  surface2:  '#111111',
  surface3:  '#1A1A1A',
  divider:   '#2E2E2E',
  border:    '#333333',

  // Surfaces (light)
  canvasLight:   '#FFFFFF',
  surfaceLight:  '#FAFAFA',
  surfaceLight2: '#F2F2F2',
  dividerLight:  '#EAEAEA',
  borderLight:   '#E1E1E1',

  // Text
  textPrimary:   '#EDEDED',
  textSecondary: '#A1A1A1',
  textTertiary:  '#707070',
  textOnLight:   '#000000',

  // Interactive
  whiteFill:  '#EDEDED', // primary button fill (dark)
  whitePress: '#CFCFCF',
  blue:       '#0070F3',
  blueHover:  '#3291FF',

  // Status (deployment state — the systemic accent)
  ready:    '#0CCE6B',
  building: '#F5A623',
  error:    '#E5484D',
  readyDim: '#0F3D2E',
  errorDim: '#3A1416',

  // Highlight (decorative only — avatar gradient / rare marketing)
  hlPurple: '#7928CA',
  hlCyan:   '#50E3C2',
  hlPink:   '#FF0080',
} as const;

export type VercelColor = keyof typeof colors;

export const stateMeta = {
  ready:    { color: colors.ready,    label: 'Ready' },
  building: { color: colors.building, label: 'Building' },
  error:    { color: colors.error,    label: 'Error' },
} as const;
```

## 2. Typography

Load Geist Sans + Geist Mono via `expo-font`. The split is by content type (prose vs machine), not user preference.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Geist-Regular':    require('../assets/fonts/Geist-Regular.ttf'),
    'Geist-Medium':     require('../assets/fonts/Geist-Medium.ttf'),
    'Geist-SemiBold':   require('../assets/fonts/Geist-SemiBold.ttf'),
    'Geist-Bold':       require('../assets/fonts/Geist-Bold.ttf'),
    'Geist-ExtraBold':  require('../assets/fonts/Geist-ExtraBold.ttf'),
    'GeistMono-Regular':require('../assets/fonts/GeistMono-Regular.ttf'),
    'GeistMono-Medium': require('../assets/fonts/GeistMono-Medium.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ contentStyle: { backgroundColor: '#000000' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#EDEDED' } satisfies TextStyle;

export const typography = {
  display:    { ...primary, fontFamily: 'Geist-ExtraBold', fontSize: 32, lineHeight: 35, letterSpacing: -0.6 },
  title:      { ...primary, fontFamily: 'Geist-Bold',      fontSize: 26, lineHeight: 30, letterSpacing: -0.4 },
  section:    { ...primary, fontFamily: 'Geist-Bold',      fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  subsection: { ...primary, fontFamily: 'Geist-SemiBold',  fontSize: 18, lineHeight: 23, letterSpacing: -0.2 },
  body:       { ...primary, fontFamily: 'Geist-Regular',   fontSize: 16, lineHeight: 24 },
  label:      { ...primary, fontFamily: 'Geist-Medium',    fontSize: 15, lineHeight: 20, letterSpacing: -0.1 },
  bodyStrong: { ...primary, fontFamily: 'Geist-SemiBold',  fontSize: 14, lineHeight: 19, letterSpacing: -0.1 },
  meta:       { color: '#A1A1A1', fontFamily: 'Geist-Regular', fontSize: 14, lineHeight: 19 },
  caption:    { color: '#A1A1A1', fontFamily: 'Geist-Medium',  fontSize: 12, lineHeight: 16, letterSpacing: 0.2 },
  tab:        { color: '#707070', fontFamily: 'Geist-Medium',  fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  button:     { fontFamily: 'Geist-SemiBold', fontSize: 14, lineHeight: 14, letterSpacing: -0.1 },
  // Mono — URLs / branches / commits / env vars / logs
  monoUrl:    { ...primary, fontFamily: 'GeistMono-Regular', fontSize: 13, lineHeight: 18 },
  monoLog:    { ...primary, fontFamily: 'GeistMono-Regular', fontSize: 12, lineHeight: 18 },
  monoTag:    { color: '#A1A1A1', fontFamily: 'GeistMono-Medium', fontSize: 10, lineHeight: 12 },
  monoBadge:  { fontFamily: 'GeistMono-Medium', fontSize: 12, lineHeight: 14 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Deployment Card (the spine of the app)

```tsx
// components/DeploymentCard.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, stateMeta } from '../theme/colors';
import { typography } from '../theme/typography';

type State = keyof typeof stateMeta;

export function DeploymentCard({
  state, env, url, branch, relativeTime,
}: { state: State; env: string; url: string; branch: string; relativeTime: string }) {
  const s = stateMeta[state];
  return (
    <View style={{
      backgroundColor: colors.surface1,
      borderWidth: 1, borderColor: colors.divider,
      borderRadius: 12, padding: 14, marginBottom: 10,
    }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, marginBottom: 8 }}>
        <View style={{
          width: 9, height: 9, borderRadius: 5, backgroundColor: s.color,
          shadowColor: s.color, shadowOpacity: 0.4, shadowRadius: 3, elevation: 0,
        }} />
        <Text style={[typography.bodyStrong, { color: state === 'ready' ? colors.textPrimary : s.color }]}>
          {s.label}
        </Text>
        <View style={{ marginLeft: 'auto' }}><EnvTag label={env} /></View>
      </View>
      <Text style={typography.monoUrl} numberOfLines={1} ellipsizeMode="middle">{url}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, marginTop: 6 }}>
        <Ionicons name="git-branch-outline" size={12} color={colors.textTertiary} />
        <Text style={[typography.monoUrl, { color: colors.textSecondary }]}>{branch}</Text>
        <Text style={[typography.meta, { marginLeft: 'auto', color: colors.textTertiary }]}>{relativeTime}</Text>
      </View>
    </View>
  );
}

export function EnvTag({ label }: { label: string }) {
  return (
    <View style={{
      backgroundColor: colors.surface3, borderWidth: 1, borderColor: colors.border,
      borderRadius: 5, paddingVertical: 3, paddingHorizontal: 7,
    }}>
      <Text style={typography.monoTag}>{label}</Text>
    </View>
  );
}
```

### Status Badge (pill)

```tsx
// components/StatusBadge.tsx
import { View, Text } from 'react-native';
import { colors, stateMeta } from '../theme/colors';
import { typography } from '../theme/typography';

export function StatusBadge({ state }: { state: keyof typeof stateMeta }) {
  const s = stateMeta[state];
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 6,
      paddingVertical: 5, paddingHorizontal: 10, borderRadius: 999,
      backgroundColor: `${s.color}14`, borderWidth: 1, borderColor: `${s.color}66`,
    }}>
      <View style={{ width: 7, height: 7, borderRadius: 4, backgroundColor: s.color }} />
      <Text style={[typography.monoBadge, { color: s.color }]}>{s.label}</Text>
    </View>
  );
}
```

### Primary / Secondary Buttons

```tsx
// components/Buttons.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.whitePress : colors.whiteFill,
        borderRadius: 8, paddingVertical: 11, paddingHorizontal: 20,
        transform: [{ scale: pressed ? 0.98 : 1 }], alignSelf: 'flex-start',
      })}
    >
      <Text style={[typography.button, { color: '#000000' }]}>{title}</Text>
    </Pressable>
  );
}

export function SecondaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.surface3 : 'transparent',
        borderWidth: 1, borderColor: colors.border,
        borderRadius: 8, paddingVertical: 10, paddingHorizontal: 18, alignSelf: 'flex-start',
      })}
    >
      <Text style={[typography.button, { color: colors.textPrimary, fontFamily: 'Geist-Medium' }]}>{title}</Text>
    </Pressable>
  );
}
```

### Build-Log Viewer

```tsx
// components/BuildLog.tsx
import { useRef, useEffect } from 'react';
import { FlatList, View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Line = { n: number; text: string; kind: 'normal' | 'error' | 'success' | 'dim' };
const kindColor = { normal: colors.textPrimary, error: colors.error, success: colors.ready, dim: colors.textTertiary };

export function BuildLog({ lines }: { lines: Line[] }) {
  const ref = useRef<FlatList<Line>>(null);
  useEffect(() => { ref.current?.scrollToEnd({ animated: true }); }, [lines.length]);
  return (
    <FlatList
      ref={ref}
      data={lines}
      keyExtractor={(l) => String(l.n)}
      style={{ backgroundColor: colors.canvas }}
      contentContainerStyle={{ paddingHorizontal: 12, paddingVertical: 10 }}
      renderItem={({ item }) => (
        <View style={{ flexDirection: 'row', gap: 12, marginBottom: 2 }}>
          <Text style={[typography.monoLog, { color: colors.textTertiary, width: 32, textAlign: 'right' }]}>{item.n}</Text>
          <Text style={[typography.monoLog, { color: kindColor[item.kind], flex: 1 }]}>{item.text}</Text>
        </View>
      )}
    />
  );
}
```

### Analytics Bar Chart (no gridlines)

```tsx
// components/AnalyticsBars.tsx
import { View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';

export function AnalyticsBars({ values }: { values: number[] }) { // values 0..1
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'flex-end', gap: 8, height: 134, padding: 12,
      backgroundColor: colors.surface2, borderWidth: 1, borderColor: colors.divider, borderRadius: 12,
    }}>
      {values.map((v, i) => (
        <LinearGradient
          key={i}
          colors={[colors.blue, 'rgba(0,112,243,0.25)']}
          style={{ flex: 1, height: Math.max(4, v * 110), borderTopLeftRadius: 4, borderTopRightRadius: 4 }}
        />
      ))}
      {/* intentionally NO gridlines / axis ticks — sparseness is the Vercel signature */}
    </View>
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
        tabBarActiveTintColor:   '#FFFFFF',   // pure white — NO tint pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(0,0,0,0.92)',
          borderTopWidth: 0.5, borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Geist-Medium', fontSize: 10, letterSpacing: 0.1 },
        headerShown: false,
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Overview',    tabBarIcon: ({ color }) => <Ionicons name="home-outline"    size={21} color={color} /> }} />
      <Tabs.Screen name="deploys"   options={{ title: 'Deployments', tabBarIcon: ({ color }) => <Ionicons name="list-outline"    size={21} color={color} /> }} />
      <Tabs.Screen name="analytics" options={{ title: 'Analytics',   tabBarIcon: ({ color }) => <Ionicons name="stats-chart-outline" size={21} color={color} /> }} />
      <Tabs.Screen name="settings"  options={{ title: 'Settings',    tabBarIcon: ({ color }) => <Ionicons name="settings-outline" size={21} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withRepeat, withSequence } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Status transition: Building → Ready (dot color cross-fade ~240ms) + soft success haptic
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Building pulse (status-dot halo loop)
const pulse = useSharedValue(1);
pulse.value = withRepeat(withSequence(withTiming(1.15, { duration: 600 }), withTiming(1.0, { duration: 600 })), -1);
const pulseStyle = useAnimatedStyle(() => ({ transform: [{ scale: pulse.value }] }));

// Button press — scale 0.98 handled inline in Pressable style({ pressed })

// Sheet present — @gorhom/bottom-sheet, snapPoints ['50%','90%'], backgroundStyle { backgroundColor: colors.surface1 }

// Tab switch — NO custom slide; default instant swap (Vercel favors immediacy)

// Pull-to-refresh — FlatList refreshControl with tintColor={colors.blue}
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The Vercel triangle is a custom SVG (`react-native-svg`), not an icon-font glyph.

| Purpose | Ionicons |
|---------|----------|
| Overview (tab) | `home-outline` |
| Deployments (tab) | `list-outline` |
| Analytics (tab) | `stats-chart-outline` |
| Settings (tab) | `settings-outline` |
| Git branch | `git-branch-outline` |
| Commit | `git-commit-outline` |
| Domain | `globe-outline` |
| Redeploy | `refresh-outline` |
| Visit / open | `open-outline` |
| Logs | `terminal-outline` |
| Copy | `copy-outline` |
| Search | `search-outline` |
| Reveal env value | `eye-outline` / `eye-off-outline` |
| Back | `chevron-back` |
| More | `ellipsis-horizontal` |
| Success | `checkmark-circle` |
| Error | `warning-outline` |

Triangle logomark via `react-native-svg`:

```tsx
import Svg, { Path } from 'react-native-svg';
export const VercelTriangle = ({ size = 22, fill = '#EDEDED' }) => (
  <Svg width={size} height={size * 0.855} viewBox="0 0 76 65">
    <Path d="M37.527 0L75.054 65H0z" fill={fill} />
  </Svg>
);
```

## 7. Platform Notes

- **Font choice**: Geist Sans + Geist Mono are SIL OFL — free to bundle. Ship both; the split is by content type, never a user setting
- **True black**: set `contentStyle.backgroundColor` and every screen root to `#000000` — NOT `#191919`. On OLED this is the brand
- **Status bar**: `<StatusBar style="light" />` always (dark-native app); `"dark"` only on the light-mode variant
- **Safe area**: wrap screens in `SafeAreaView`; the blurred tab bar floats over content — pad list bottoms by tab height + safe area
- **No shadows on cards**: React Native shadows render poorly on pure black anyway — use 1pt `#2E2E2E` borders for all card separation
- **Dynamic Type**: set `allowFontScaling={false}` on tab labels, env tags, mono badges, and ≤12pt mono captions (layout-sensitive in dense rows); allow scaling on body/headings
- **Keyboard**: use `KeyboardAvoidingView` on env-var and search forms
- **Dark mode**: dark IS the default. If supporting light, swap to `canvasLight`/`surfaceLight`/`dividerLight` via `useColorScheme()`; status colors stay identical
- **Logs performance**: use `FlatList` (not `ScrollView`) for build logs — they can be thousands of lines; `scrollToEnd` on append
- **Accessibility**: pair every status color with its text label; announce deployment cards as a single composed string so the decorative dot isn't read separately
