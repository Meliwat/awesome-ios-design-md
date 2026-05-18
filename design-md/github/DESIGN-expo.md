# GitHub (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates GitHub's visual language (the Primer design system) into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur` (tab bar), and `react-native-reanimated` v3.

> GitHub is **semantic and disciplined**. There is one accent blue (`#2F81F7`), one reserved green (`#238636`) for **Code** / **Merge** only, state colors that carry fixed meaning (open=green, merged=purple, closed=red, draft=grey), the iconic contributions heatmap ramp, and **monospace for all code, SHAs, file/branch names, and diffs**. Dark (`#0D1117`) is the default; light is a first-class peer.

## 1. Color Tokens

```ts
// theme/colors.ts
export const dark = {
  // Canvas & surfaces
  canvas:        '#0D1117', // primary — blue-tinted near-black
  canvasSubtle:  '#161B22', // raised surfaces, card headers, heatmap L0
  canvasInset:   '#21262D', // default button, code-block bg, count pills
  surfacePressed:'#30363D',
  borderDefault: '#30363D',
  borderMuted:   '#21262D',

  // Text (foreground)
  fg:        '#E6EDF3',
  fgMuted:   '#7D8590',
  fgSubtle:  '#6E7681',
  fgOnEmphasis: '#FFFFFF',

  // Interactive
  accentFg:       '#2F81F7', // links, owner/name, active text
  accentEmphasis: '#1F6FEB',
  btnPrimary:     '#238636', // RESERVED — Code / Merge only
  btnPrimaryHover:'#2EA043',
  btnDanger:      '#DA3633',

  // Semantic state
  open:      '#3FB950', // open issue/PR, passing
  merged:    '#A371F7', // merged PR (purple = done)
  closed:    '#F85149', // closed issue/PR, failing, delete
  draft:     '#6E7681', // draft/neutral
  attention: '#D29922', // pending, warning
  sponsor:   '#DB61A2',

  // Tab-strip active underline (orange-coral)
  tabUnderline: '#F78166',

  // Diff
  diffAddBg: 'rgba(63,185,80,0.15)',
  diffDelBg: 'rgba(248,81,73,0.15)',
} as const;

export const light = {
  canvas:        '#FFFFFF',
  canvasSubtle:  '#F6F8FA',
  canvasInset:   '#F6F8FA',
  surfacePressed:'#EAEEF2',
  borderDefault: '#D0D7DE',
  borderMuted:   '#D8DEE4',
  fg:        '#1F2328',
  fgMuted:   '#656D76',
  fgSubtle:  '#6E7781',
  fgOnEmphasis: '#FFFFFF',
  accentFg:       '#0969DA',
  accentEmphasis: '#0969DA',
  btnPrimary:     '#1F883D',
  btnPrimaryHover:'#1A7F37',
  btnDanger:      '#CF222E',
  open:      '#1A7F37',
  merged:    '#8250DF',
  closed:    '#CF222E',
  draft:     '#6E7781',
  attention: '#9A6700',
  sponsor:   '#BF3989',
  tabUnderline: '#FD8C73',
  diffAddBg: 'rgba(26,127,55,0.15)',
  diffDelBg: 'rgba(207,34,46,0.15)',
} as const;

// Contributions heatmap ramp — the iconic GitHub visual (do not alter the stops)
export const heatmap = {
  dark:  ['#161B22', '#0E4429', '#006D32', '#26A641', '#39D353'],
  light: ['#EBEDF0', '#9BE9A8', '#40C463', '#30A14E', '#216E39'],
} as const;
```

## 2. Typography

Mona Sans (GitHub's variable grotesque, SIL OFL) for UI; a monospace for **all** code, SHAs, file/branch names, diffs. Bundle both via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'MonaSans-Regular':  require('../assets/fonts/MonaSans-Regular.ttf'),
    'MonaSans-Medium':   require('../assets/fonts/MonaSans-Medium.ttf'),
    'MonaSans-SemiBold': require('../assets/fonts/MonaSans-SemiBold.ttf'),
    'MonaSans-Bold':     require('../assets/fonts/MonaSans-Bold.ttf'),
    'MonaSans-ExtraBold':require('../assets/fonts/MonaSans-ExtraBold.ttf'),
    'JetBrainsMono-Regular':  require('../assets/fonts/JetBrainsMono-Regular.ttf'),
    'JetBrainsMono-SemiBold': require('../assets/fonts/JetBrainsMono-SemiBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const fg = { color: '#E6EDF3' } satisfies TextStyle;
const SANS = 'MonaSans';
const MONO = 'JetBrainsMono';

export const typography = {
  display:     { ...fg, fontFamily: `${SANS}-ExtraBold`, fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  repoTitle:   { ...fg, fontFamily: `${SANS}-Bold`,      fontSize: 24, lineHeight: 29, letterSpacing: -0.3 },
  section:     { ...fg, fontFamily: `${SANS}-SemiBold`,  fontSize: 20, lineHeight: 26, letterSpacing: -0.2 },
  subtitle:    { ...fg, fontFamily: `${SANS}-SemiBold`,  fontSize: 16, lineHeight: 22 },
  body:        { ...fg, fontFamily: `${SANS}-Regular`,   fontSize: 14, lineHeight: 21 },
  bodyStrong:  { ...fg, fontFamily: `${SANS}-SemiBold`,  fontSize: 14, lineHeight: 21 },
  code:        { ...fg, fontFamily: `${MONO}-Regular`,   fontSize: 13, lineHeight: 19 },
  codeInline:  { ...fg, fontFamily: `${MONO}-Regular`,   fontSize: 13, lineHeight: 18 },
  sha:         { ...fg, fontFamily: `${MONO}-SemiBold`,  fontSize: 12, lineHeight: 12 },
  metadata:    { color: '#7D8590', fontFamily: `${SANS}-Regular`,  fontSize: 12, lineHeight: 17 },
  label:       { ...fg, fontFamily: `${SANS}-Medium`,    fontSize: 12, lineHeight: 12 },
  countPill:   { color: '#6E7681', fontFamily: `${SANS}-SemiBold`, fontSize: 11, lineHeight: 11 },
  tab:         { color: '#7D8590', fontFamily: `${SANS}-Medium`,   fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  caption:     { color: '#7D8590', fontFamily: `${SANS}-Regular`,  fontSize: 11, lineHeight: 14 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Repo Header (`owner / name` + stats + tab strip)

```tsx
// components/RepoHeader.tsx
import { View, Text, ScrollView, Pressable } from 'react-native';
import Octicons from '@expo/vector-icons/Octicons';
import { dark } from '../theme/colors';
import { typography } from '../theme/typography';

const TABS = ['Code', 'Issues', 'Pull requests', 'Actions'] as const;

export function RepoHeader({
  owner, name, description, language, languageColor, stars, forks, activeTab, onTab,
}: {
  owner: string; name: string; description: string;
  language: string; languageColor: string; stars: string; forks: string;
  activeTab: number; onTab: (i: number) => void;
}) {
  return (
    <View style={{ backgroundColor: dark.canvas, paddingTop: 12 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, paddingHorizontal: 16 }}>
        <Octicons name="repo" size={17} color={dark.fgMuted} />
        <Text style={[typography.subtitle, { color: dark.accentFg }]}>{owner}</Text>
        <Text style={[typography.subtitle, { color: dark.fgMuted }]}>/</Text>
        <Text style={[typography.subtitle, { color: dark.accentFg, fontFamily: 'MonaSans-Bold' }]}>{name}</Text>
      </View>
      <Text style={[typography.body, { color: dark.fgMuted, paddingHorizontal: 16, marginTop: 6 }]}>{description}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 16, paddingHorizontal: 16, marginTop: 10 }}>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5 }}>
          <View style={{ width: 11, height: 11, borderRadius: 6, backgroundColor: languageColor }} />
          <Text style={typography.metadata}>{language}</Text>
        </View>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
          <Octicons name="star" size={13} color={dark.fgMuted} />
          <Text style={typography.metadata}>{stars}</Text>
        </View>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
          <Octicons name="repo-forked" size={13} color={dark.fgMuted} />
          <Text style={typography.metadata}>{forks}</Text>
        </View>
      </View>
      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ paddingHorizontal: 16, gap: 20, marginTop: 14 }}>
        {TABS.map((t, i) => {
          const active = i === activeTab;
          return (
            <Pressable key={t} onPress={() => onTab(i)} style={{ paddingBottom: 10, borderBottomWidth: 2, borderBottomColor: active ? dark.tabUnderline : 'transparent' }}>
              <Text style={[typography.body, { color: active ? dark.fg : dark.fgMuted, fontFamily: active ? 'MonaSans-SemiBold' : 'MonaSans-Regular' }]}>{t}</Text>
            </Pressable>
          );
        })}
      </ScrollView>
      <View style={{ height: 1, backgroundColor: dark.borderDefault }} />
    </View>
  );
}
```

### Branch Pill + Latest-Commit Strip

```tsx
// components/RepoMeta.tsx
import { View, Text, Image } from 'react-native';
import Octicons from '@expo/vector-icons/Octicons';
import { dark } from '../theme/colors';
import { typography } from '../theme/typography';

export function BranchPill({ branch }: { branch: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, alignSelf: 'flex-start', backgroundColor: dark.canvasSubtle, borderWidth: 1, borderColor: dark.borderDefault, borderRadius: 6, paddingHorizontal: 12, paddingVertical: 7 }}>
      <Octicons name="git-branch" size={13} color={dark.fgMuted} />
      <Text style={typography.sha}>{branch}</Text>
    </View>
  );
}

export function LatestCommit({ avatarUri, message, sha, when }: { avatarUri: string; message: string; sha: string; when: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, backgroundColor: dark.canvasSubtle, borderBottomWidth: 1, borderBottomColor: dark.borderDefault, padding: 12 }}>
      <Image source={{ uri: avatarUri }} style={{ width: 18, height: 18, borderRadius: 9 }} />
      <Text style={[typography.body, { flex: 1 }]} numberOfLines={1}>{message}</Text>
      <Text style={[typography.caption, { fontFamily: 'JetBrainsMono-Regular' }]}>{sha}</Text>
      <Text style={typography.caption}>{when}</Text>
    </View>
  );
}
```

### Code Browser (monospace blob + sticky path breadcrumb)

```tsx
// components/CodeBrowser.tsx
import { View, Text, ScrollView } from 'react-native';
import { dark } from '../theme/colors';
import { typography } from '../theme/typography';

export function CodeBrowser({ path, lines }: { path: string; lines: string[] }) {
  return (
    <View style={{ flex: 1, backgroundColor: dark.canvas }}>
      {/* Sticky path breadcrumb — monospace */}
      <View style={{ paddingHorizontal: 16, paddingVertical: 10, borderBottomWidth: 1, borderBottomColor: dark.borderDefault, backgroundColor: dark.canvasSubtle }}>
        <Text style={[typography.code, { color: dark.fgMuted }]}>{path}</Text>
      </View>
      <ScrollView horizontal>
        <ScrollView>
          <View style={{ backgroundColor: dark.canvasSubtle, padding: 12 }}>
            {lines.map((ln, i) => (
              <View key={i} style={{ flexDirection: 'row' }}>
                <Text style={[typography.code, { color: dark.fgSubtle, width: 36, textAlign: 'right', marginRight: 16 }]}>{i + 1}</Text>
                <Text style={typography.code}>{ln}</Text>
              </View>
            ))}
          </View>
        </ScrollView>
      </ScrollView>
    </View>
  );
}
```

### State Pill (open / merged / closed / draft) — color carries meaning

```tsx
// components/StatePill.tsx
import { View, Text } from 'react-native';
import Octicons from '@expo/vector-icons/Octicons';
import { dark } from '../theme/colors';
import { typography } from '../theme/typography';

const MAP = {
  open:   { color: dark.open,   icon: 'issue-opened' as const },
  merged: { color: dark.merged, icon: 'git-merge' as const },
  closed: { color: dark.closed, icon: 'issue-closed' as const },
  draft:  { color: dark.draft,  icon: 'git-pull-request-draft' as const },
};

export function StatePill({ state }: { state: keyof typeof MAP }) {
  const m = MAP[state];
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5, alignSelf: 'flex-start', backgroundColor: m.color, borderRadius: 999, paddingHorizontal: 12, paddingVertical: 5 }}>
      <Octicons name={m.icon} size={12} color="#FFFFFF" />
      <Text style={[typography.label, { color: '#FFFFFF', textTransform: 'capitalize' }]}>{state}</Text>
    </View>
  );
}
```

### Contributions Heatmap (signature)

```tsx
// components/Heatmap.tsx
import { View, Text } from 'react-native';
import { heatmap, dark } from '../theme/colors';
import { typography } from '../theme/typography';

export function Heatmap({ weeks }: { weeks: number[][] /* [week][day] -> level 0..4 */ }) {
  return (
    <View>
      <View style={{ flexDirection: 'row', gap: 3 }}>
        {weeks.map((week, wi) => (
          <View key={wi} style={{ gap: 3 }}>
            {week.map((level, di) => (
              <View key={di} style={{
                width: 11, height: 11, borderRadius: 2,
                backgroundColor: heatmap.dark[level],
                borderWidth: level === 0 ? 1 : 0, borderColor: 'rgba(110,118,129,0.15)',
              }} />
            ))}
          </View>
        ))}
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginTop: 8 }}>
        <Text style={typography.caption}>Less</Text>
        {heatmap.dark.map((c, i) => <View key={i} style={{ width: 11, height: 11, borderRadius: 2, backgroundColor: c }} />)}
        <Text style={typography.caption}>More</Text>
      </View>
    </View>
  );
}
```

### Diff Viewer (additions green wash / deletions red wash)

```tsx
// components/DiffViewer.tsx
import { View, Text, ScrollView } from 'react-native';
import { dark } from '../theme/colors';
import { typography } from '../theme/typography';

type Line = { kind: 'add' | 'del' | 'ctx'; text: string };

export function DiffViewer({ file, adds, dels, lines }: { file: string; adds: number; dels: number; lines: Line[] }) {
  return (
    <View style={{ borderWidth: 1, borderColor: dark.borderDefault, borderRadius: 6, overflow: 'hidden' }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, backgroundColor: dark.canvasSubtle, padding: 10, borderBottomWidth: 1, borderBottomColor: dark.borderDefault }}>
        <Text style={[typography.code, { flex: 1 }]}>{file}</Text>
        <Text style={[typography.code, { color: dark.open }]}>+{adds}</Text>
        <Text style={[typography.code, { color: dark.closed }]}>-{dels}</Text>
      </View>
      <ScrollView horizontal>
        <View style={{ backgroundColor: dark.canvasSubtle }}>
          {lines.map((l, i) => (
            <View key={i} style={{ flexDirection: 'row', backgroundColor: l.kind === 'add' ? dark.diffAddBg : l.kind === 'del' ? dark.diffDelBg : 'transparent' }}>
              <Text style={[typography.code, { color: dark.fgSubtle, width: 28, textAlign: 'center' }]}>{l.kind === 'add' ? '+' : l.kind === 'del' ? '-' : ' '}</Text>
              <Text style={[typography.code, { paddingRight: 16 }]}>{l.text}</Text>
            </View>
          ))}
        </View>
      </ScrollView>
    </View>
  );
}
```

### Buttons (reserved green + default)

```tsx
// components/Buttons.tsx
import { Pressable, Text, View } from 'react-native';
import Octicons from '@expo/vector-icons/Octicons';
import { dark } from '../theme/colors';
import { typography } from '../theme/typography';

// RESERVED — only for "Code" download and "Merge pull request"
export function GreenButton({ title, icon, onPress }: { title: string; icon: keyof typeof Octicons.glyphMap; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 8,
        backgroundColor: pressed ? dark.btnPrimaryHover : dark.btnPrimary,
        borderRadius: 6, paddingVertical: 10, paddingHorizontal: 16,
      })}
    >
      <Octicons name={icon} size={16} color="#FFFFFF" />
      <Text style={[typography.body, { color: '#FFFFFF', fontFamily: 'MonaSans-SemiBold' }]}>{title}</Text>
    </Pressable>
  );
}

export function DefaultButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? dark.surfacePressed : dark.canvasInset,
        borderWidth: 1, borderColor: dark.borderDefault,
        borderRadius: 6, paddingVertical: 10, paddingHorizontal: 16,
      })}
    >
      <Text style={[typography.body, { fontFamily: 'MonaSans-SemiBold' }]}>{title}</Text>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

4 tabs, translucent blur over `rgba(13,17,23,0.94)`, 1px top border `#21262D`, no tint pill; active `#E6EDF3`, inactive `#7D8590`.

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Octicons from '@expo/vector-icons/Octicons';
import { BlurView } from 'expo-blur';
import { dark } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: dark.fg,
        tabBarInactiveTintColor: dark.fgMuted,
        tabBarStyle: { position: 'absolute', borderTopWidth: 1, borderTopColor: dark.borderMuted, backgroundColor: 'transparent' },
        tabBarBackground: () => <BlurView intensity={30} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(13,17,23,0.94)' }} />,
        tabBarLabelStyle: { fontFamily: 'MonaSans-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"         options={{ title: 'Home',          tabBarIcon: ({ color }) => <Octicons name="home"  size={22} color={color} /> }} />
      <Tabs.Screen name="notifications" options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Octicons name="bell"  size={22} color={color} /> }} />
      <Tabs.Screen name="explore"       options={{ title: 'Explore',       tabBarIcon: ({ color }) => <Octicons name="telescope" size={22} color={color} /> }} />
      <Tabs.Screen name="profile"       options={{ title: 'Profile',       tabBarIcon: ({ color }) => <Octicons name="person" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Tab-strip underline slide
// Animate borderBottom indicator x/width with withTiming(…, { duration: 200 })

// Merge button → "Merged" state
// Crossfade label + bg from #238636 to #A371F7 over 200ms (Reduce Motion → instant swap)

// Star count-up
// Animate the count number from old → new over 250ms; Reduce Motion → instant set

// Diff "Expand" hidden lines
// Layout animation: Animated height 0 → content; Reduce Motion → instant insert

// Pull-to-refresh
// Standard RefreshControl tinted dark.fgMuted

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // on Merge confirm
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);              // on Star toggle
```

## 6. Icon Library

Use `@expo/vector-icons` **Octicons** — GitHub's own icon set ships as Octicons, so this is a near-exact match.

| Purpose | Octicons |
|---------|----------|
| Repo | `repo` |
| Code / `<>` | `code` |
| Issues | `issue-opened` / `issue-closed` |
| Pull requests | `git-pull-request` |
| Merged | `git-merge` |
| Draft PR | `git-pull-request-draft` |
| Branch | `git-branch` |
| Commit | `git-commit` |
| Star | `star` / `star-fill` |
| Fork | `repo-forked` |
| Folder (tree) | `file-directory-fill` |
| File (tree) | `file` |
| Home (tab) | `home` |
| Notifications (tab) | `bell` |
| Explore (tab) | `telescope` |
| Profile (tab) | `person` |
| Back | `chevron-left` |
| Search | `search` |
| Watch | `eye` |
| Download (Code btn) | `download` |
| Sponsor | `heart` |

## 7. Platform Notes

- **Font choice**: Mona Sans (SIL OFL) is safe to bundle; for the monospace, `JetBrains Mono` is a clean OFL stand-in for GitHub's Monaspace (`SF Mono` is the iOS system default if you prefer not to bundle).
- **Monospace contract**: code, SHAs, file/branch names, and diffs MUST be monospace — never substitute the sans face; glyph distinction (`0`/`O`, `1`/`l`) is a correctness requirement for developers.
- **Reserved green**: `#238636` (`#1F883D` light) is ONLY the "Code" download button and "Merge pull request" — never style another control with it; it's a learned "download / merge" affordance. Everything else uses the default `#21262D` button.
- **Theme**: dark `#0D1117` is the default; use `useColorScheme()` to swap to the `light` token set (light is a first-class peer, not an afterthought).
- **Status bar**: `<StatusBar style="light" />` on dark, `"dark"` on light.
- **Safe area + tab bar**: the translucent tab bar needs `expo-blur`; pad scroll content by the tab bar height + safe-area inset so the last row isn't hidden.
- **Heatmap ramp**: never alter the 5 stops — `#161B22 → #0E4429 → #006D32 → #26A641 → #39D353` (dark) is the single most recognizable GitHub visual.
- **State color is meaning, not decoration**: open=green, merged=purple, closed=red, draft=grey are fixed; also carry a leading glyph + text label so color-blind users get the state.
- **Dynamic Type**: allow scaling on titles/body/code (code stays monospace as it scales); set `allowFontScaling={false}` on tab labels, count pills, SHAs (table/tree-layout-sensitive).
- **Accessibility**: diff additions/deletions carry `+`/`-` markers (not only the green/red wash); label state pills with both the glyph and the word; ensure `#7D8590` muted text is ≥12pt for AA contrast on `#0D1117`.
