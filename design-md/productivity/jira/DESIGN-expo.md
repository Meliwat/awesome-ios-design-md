# Jira (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Jira's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3 (plus `react-native-gesture-handler` for board drag).

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand (interactive)
  jiraBlue:        '#0052CC',
  jiraBlueBold:    '#1868DB',
  jiraBluePressed: '#09326C',
  link:            '#0C66E4',
  linkDark:        '#579DFF',
  inkNavy:         '#172B4D',

  // Surfaces (light)
  canvas:       '#FFFFFF',
  sunken:       '#F7F8F9',
  surfaceHover: '#F1F2F4',
  divider:      'rgba(9,30,66,0.14)',
  border:       'rgba(9,30,66,0.06)',

  // Surfaces (dark)
  darkCanvas:   '#1D2125',
  darkSurface1: '#22272B',
  darkSurface2: '#2C333A',
  darkSurface3: '#38414A',
  darkDivider:  '#38414A',

  // Text
  textPrimary:   '#172B4D',
  textSubtle:    '#44546F',
  textSubtlest:  '#626F86',
  darkTextPrimary:  '#C7D1DB',
  darkTextSubtle:   '#9FADBC',
  darkTextSubtlest: '#738496',

  // Issue-type accents
  storyGreen: '#1F845A',
  bugRed:     '#C9372C',
  taskBlue:   '#1868DB',
  epicPurple: '#8270DB',
  subtaskGray:'#5E6C84',

  // Avatar / label palette
  avPurple:  '#5E4DB2',
  avTeal:    '#1D7F8C',
  avMagenta: '#943D73',
  avOrange:  '#A54800',
} as const;

// Semantic status pairs — bg + text, light & dark
export const statusPairs = {
  todo:       { bg: '#DCDFE4', text: '#44546F', bgD: '#38414A', textD: '#9FADBC', label: 'TO DO' },
  inProgress: { bg: '#E9F2FF', text: '#0055CC', bgD: '#092957', textD: '#8FB8F6', label: 'IN PROGRESS' },
  done:       { bg: '#DCFFF1', text: '#216E4E', bgD: '#1C3329', textD: '#7EE2B8', label: 'DONE' },
  blocked:    { bg: '#FFECEB', text: '#AE2A19', bgD: '#5D1F1A', textD: '#FD9891', label: 'BLOCKED' },
  warning:    { bg: '#FFF7D6', text: '#7F5F01', bgD: '#3D3000', textD: '#F5CD47', label: 'ATTENTION' },
  epic:       { bg: '#F3F0FF', text: '#5E4DB2', bgD: '#352C63', textD: '#B8ACF6', label: 'EPIC' },
} as const;

export type StatusKey = keyof typeof statusPairs;
```

## 2. Typography

Load **Atlassian Sans** + **Atlassian Mono** via `expo-font` (or substitute **Inter**, SIL OFL). Tabular numerals via `fontVariant: ['tabular-nums']`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Atlassian-Regular':  require('../assets/fonts/AtlassianSans-Regular.ttf'),
    'Atlassian-Medium':   require('../assets/fonts/AtlassianSans-Medium.ttf'),
    'Atlassian-Semibold': require('../assets/fonts/AtlassianSans-SemiBold.ttf'),
    'Atlassian-Bold':     require('../assets/fonts/AtlassianSans-Bold.ttf'),
    'Atlassian-Mono':     require('../assets/fonts/AtlassianMono-Regular.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tnum = { fontVariant: ['tabular-nums'] as const };
const primary = { color: '#172B4D' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...primary, fontFamily: 'Atlassian-Bold',     fontSize: 30, lineHeight: 36, letterSpacing: -0.4 },
  boardTitle:  { ...primary, fontFamily: 'Atlassian-Bold',     fontSize: 22, lineHeight: 28, letterSpacing: -0.3 },
  issueTitle:  { ...primary, fontFamily: 'Atlassian-Semibold', fontSize: 20, lineHeight: 26, letterSpacing: -0.2 },
  subsection:  { ...primary, fontFamily: 'Atlassian-Semibold', fontSize: 16, lineHeight: 22 },
  body:        { ...primary, fontFamily: 'Atlassian-Regular',  fontSize: 16, lineHeight: 24 },
  cardSummary: { ...primary, fontFamily: 'Atlassian-Medium',   fontSize: 15, lineHeight: 21 },
  meta:        { color: '#626F86', fontFamily: 'Atlassian-Regular', fontSize: 14, lineHeight: 19 },
  fieldLabel:  { color: '#44546F', fontFamily: 'Atlassian-Semibold', fontSize: 12, lineHeight: 16, letterSpacing: 0.4 },
  columnHead:  { color: '#626F86', fontFamily: 'Atlassian-Bold', fontSize: 12, lineHeight: 16, letterSpacing: 0.6 },
  lozenge:     { fontFamily: 'Atlassian-Bold', fontSize: 11, lineHeight: 12, letterSpacing: 0.4 },
  issueKey:    { ...tnum, color: '#626F86', fontFamily: 'Atlassian-Bold', fontSize: 11, lineHeight: 12, letterSpacing: 0.2 },
  tab:         { color: '#626F86', fontFamily: 'Atlassian-Medium', fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
  code:        { ...primary, fontFamily: 'Atlassian-Mono', fontSize: 13, lineHeight: 20 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Status Lozenge

```tsx
// components/StatusLozenge.tsx
import { Text, View } from 'react-native';
import { statusPairs, type StatusKey } from '../theme/colors';
import { typography } from '../theme/typography';

export function StatusLozenge({ status, dark = false }: { status: StatusKey; dark?: boolean }) {
  const p = statusPairs[status];
  return (
    <View style={{
      alignSelf: 'flex-start',
      backgroundColor: dark ? p.bgD : p.bg,
      borderRadius: 3,
      paddingVertical: 4,
      paddingHorizontal: 8,
    }}>
      <Text style={[typography.lozenge, { color: dark ? p.textD : p.text }]}>{p.label}</Text>
    </View>
  );
}
```

### Issue-Type Icon

```tsx
// components/IssueTypeIcon.tsx
import { View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

const map = {
  story:   { c: colors.storyGreen, icon: 'bookmark' as const },
  bug:     { c: colors.bugRed,     icon: 'bug' as const },
  task:    { c: colors.taskBlue,   icon: 'checkmark' as const },
  epic:    { c: colors.epicPurple, icon: 'flash' as const },
  subtask: { c: colors.subtaskGray,icon: 'return-down-forward' as const },
};

export function IssueTypeIcon({ type, size = 16 }: { type: keyof typeof map; size?: number }) {
  const m = map[type];
  return (
    <View style={{ width: size, height: size, borderRadius: 3, backgroundColor: m.c, alignItems: 'center', justifyContent: 'center' }}>
      <Ionicons name={m.icon} size={size * 0.62} color="#FFF" />
    </View>
  );
}
```

### Issue Card

```tsx
// components/IssueCard.tsx
import { Text, View } from 'react-native';
import { IssueTypeIcon } from './IssueTypeIcon';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Label = { text: string; bg: string; fg: string };

export function IssueCard({
  type, issueKey, summary, labels = [], points, initials, avatarColor, dark = false,
}: {
  type: 'story' | 'bug' | 'task' | 'epic' | 'subtask';
  issueKey: string; summary: string; labels?: Label[]; points: number;
  initials: string; avatarColor: string; dark?: boolean;
}) {
  return (
    <View style={{
      backgroundColor: dark ? colors.darkSurface1 : colors.canvas,
      borderRadius: 8,
      borderWidth: 1,
      borderColor: dark ? colors.darkDivider : colors.border,
      padding: 12,
      shadowColor: colors.inkNavy,
      shadowOpacity: dark ? 0 : 0.13,
      shadowRadius: 1,
      shadowOffset: { width: 0, height: 1 },
      elevation: dark ? 0 : 1,
    }}>
      <Text style={[typography.cardSummary, dark && { color: colors.darkTextPrimary }]} numberOfLines={2}>
        {summary}
      </Text>

      {labels.length > 0 && (
        <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 5, marginTop: 10 }}>
          {labels.map((l, i) => (
            <View key={i} style={{ backgroundColor: l.bg, borderRadius: 3, paddingVertical: 2, paddingHorizontal: 7 }}>
              <Text style={{ fontFamily: 'Atlassian-Bold', fontSize: 10, letterSpacing: 0.3, color: l.fg }}>
                {l.text.toUpperCase()}
              </Text>
            </View>
          ))}
        </View>
      )}

      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', marginTop: 12 }}>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
          <IssueTypeIcon type={type} />
          <Text style={typography.issueKey}>{issueKey}</Text>
        </View>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
          <View style={{ minWidth: 18, height: 18, borderRadius: 9, paddingHorizontal: 5, alignItems: 'center', justifyContent: 'center', backgroundColor: dark ? colors.darkSurface2 : '#DDE1E6' }}>
            <Text style={{ fontFamily: 'Atlassian-Bold', fontSize: 11, fontVariant: ['tabular-nums'], color: dark ? colors.darkTextSubtle : colors.textSubtle }}>{points}</Text>
          </View>
          <View style={{ width: 22, height: 22, borderRadius: 11, backgroundColor: avatarColor, alignItems: 'center', justifyContent: 'center' }}>
            <Text style={{ fontFamily: 'Atlassian-Bold', fontSize: 10, color: '#FFF' }}>{initials}</Text>
          </View>
        </View>
      </View>
    </View>
  );
}
```

### Board Column

```tsx
// components/BoardColumn.tsx
import { ScrollView, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BoardColumn({ name, count, children }: { name: string; count: number; children: React.ReactNode }) {
  return (
    <View style={{ width: 248 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, paddingBottom: 10 }}>
        <Text style={typography.columnHead}>{name.toUpperCase()}</Text>
        <View style={{ backgroundColor: colors.surfaceHover, borderRadius: 999, paddingHorizontal: 7, paddingVertical: 1 }}>
          <Text style={{ fontFamily: 'Atlassian-Bold', fontSize: 11, fontVariant: ['tabular-nums'], color: colors.textSubtlest }}>{count}</Text>
        </View>
      </View>
      <ScrollView showsVerticalScrollIndicator={false} contentContainerStyle={{ gap: 8 }}>
        {children}
      </ScrollView>
    </View>
  );
}
```

### Field Row (issue detail)

```tsx
// components/FieldRow.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FieldRow({ label, children }: { label: string; children: React.ReactNode }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', minHeight: 44,
      borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      <Text style={[typography.fieldLabel, { width: 110 }]}>{label.toUpperCase()}</Text>
      <View style={{ flex: 1 }}>{children}</View>
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
        tabBarActiveTintColor: colors.jiraBlue,
        tabBarInactiveTintColor: colors.textSubtlest,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Atlassian-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="boards"        options={{ title: 'Boards',    tabBarIcon: ({ color }) => <Ionicons name="grid"           size={24} color={color} /> }} />
      <Tabs.Screen name="backlog"       options={{ title: 'Backlog',   tabBarIcon: ({ color }) => <Ionicons name="list"           size={24} color={color} /> }} />
      <Tabs.Screen name="search"        options={{ title: 'Search',    tabBarIcon: ({ color }) => <Ionicons name="search"         size={24} color={color} /> }} />
      <Tabs.Screen name="notifications" options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Ionicons name="notifications-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="you"           options={{ title: 'You',       tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Card drag — long-press then lift
const lift = useSharedValue(0);
const cardStyle = useAnimatedStyle(() => ({
  transform: [{ scale: 1 + lift.value * 0.02 }, { rotateZ: `${lift.value * 2}deg` }],
  shadowOpacity: 0.13 + lift.value * 0.02,
  shadowRadius: 1 + lift.value * 7,
}));
// onLongPress: lift.value = withTiming(1, { duration: 200 })
// onDrop:      lift.value = withTiming(0, { duration: 150 });
//              Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Status lozenge dropdown — FadeIn 150 + 4px slide
// entering={FadeIn.duration(150)} exiting={FadeOut.duration(150)}

// Backlog section collapse
const rot = useSharedValue(0); // 0 -> 90 with withTiming(90, { duration: 150 })

// Sprint progress segments fill on load
// withTiming(targetWidth, { duration: 400 })

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); // card drop, status change
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);  // filter toggle, section collapse
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Jira's issue-type glyphs map cleanly to filled Ionicons inside a rounded color container.

| Purpose | Ionicons |
|---------|----------|
| Boards (tab) | `grid` |
| Backlog (tab) | `list` |
| Search (tab) | `search` |
| Notifications (tab) | `notifications-outline` |
| You (tab) | `person-circle-outline` |
| Story type | `bookmark` |
| Bug type | `bug` |
| Task type | `checkmark` |
| Epic type | `flash` |
| Sub-task type | `return-down-forward` |
| Status dropdown | `chevron-down` |
| Priority Highest | `chevron-up` (doubled) |
| Priority Medium | `remove` |
| Filter | `funnel-outline` |
| Overflow | `ellipsis-horizontal` |
| Watch | `eye-outline` |
| Add / create | `add` |
| Flag | `flag` |
| Comment | `chatbubble-outline` |
| Attachment | `attach` |
| Sprint / time | `time-outline` |

## 7. Platform Notes

- **Font choice**: Atlassian Sans/Mono are proprietary — if you cannot license them for distribution, ship **Inter** (SIL OFL) and keep `fontVariant: ['tabular-nums']` on issue keys, points, and counts so columns align
- **Tabular numerals**: critical for the board — apply `fontVariant: ['tabular-nums']` to every numeric (points, counts, keys)
- **Status bar**: `<StatusBar style="dark" />` on light, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; the board can bleed columns to the screen edge but the tab bar needs safe-area padding
- **Board drag-and-drop**: use `react-native-gesture-handler` `LongPressGestureHandler` → `PanGestureHandler`, or `react-native-draggable-flatlist` per column with a shared drag context across columns; medium haptic on drop, then update the card's status lozenge
- **Dynamic Type**: set `allowFontScaling={false}` on lozenges, label chips, issue keys, column headers, and tab labels (board column width is fixed); allow scaling on titles/body/comments
- **Dark mode**: use `useColorScheme()`; swap to `darkCanvas`/`darkSurface1-3`, brighten interactive blue to `jiraBlueBold`, and replace navy card shadows with the surface step + 1px border
- **Keyboard**: wrap the comment composer / inline-edit in `KeyboardAvoidingView`; keep board scroll position when keyboard dismisses
- **Accessibility**: give cards an `accessibilityLabel` of "{type}, {key}, {summary}, {points} points, {assignee}"; expose drag via `accessibilityActions` ("Move to column") so it's reachable without long-press; never convey status by color alone — the lozenge text label carries it
