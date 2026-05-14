# Perplexity (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Perplexity's visual language into paste-ready Expo / React Native code: a design-token module, themed components for the search input, source cards, citation chips, the answer block, the Pro Search toggle, and Reanimated streaming.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `react-native-svg`, `react-native-markdown-display`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts — Perplexity brand palette (dark-first, single teal accent)
export const colors = {
  // Canvas (dark by default)
  canvas:        '#0A0A0A',
  surface1:      '#171717',  // input, source cards
  surface2:      '#1F1F1F',  // chips, citation chip
  surface3:      '#2A2A2A',  // sheets, divider
  divider:       '#2A2A2A',

  // Text
  textPrimary:   '#FAFAFA',  // off-white, never pure white
  textSecondary: '#A1A1A1',
  textTertiary:  '#6E6E6E',
  textMuted:     '#4A4A4A',

  // Perplexity Teal — the single signature accent
  teal:          '#20B8CD',
  tealBright:    '#3DD6EC',  // streaming cursor
  tealDeep:      '#1591A3',  // pressed
  tealSoft:      '#0F3A42',  // Pro Steps fill, citation chip focused bg
  tealLight:     '#1591A3',  // WCAG-safe teal on light mode

  // Code & syntax
  codeBg:        '#0E0E0E',
  codeFg:        '#E5E5E5',
  syntaxString:  '#A8E063',  // lime
  syntaxNumber:  '#F2A65A',  // soft orange
  syntaxFunction: '#B988F2', // soft purple
  // syntaxKeyword reuses teal

  // Semantic
  success:       '#22C55E',
  warning:       '#F59E0B',
  error:         '#EF4444',
  proGold:       '#E0B341',

  // Light mode
  lightCanvas:   '#FFFFFF',
  lightSurface1: '#F7F7F7',
  lightSurface2: '#EFEFEF',
  lightDivider:  '#E5E5E5',
  lightTextPri:  '#111111',
  lightTextSec:  '#555555',
  lightTextTer:  '#999999',

  // Shadow tokens (high opacity on dark)
  shadowSubtle:  'rgba(0, 0, 0, 0.4)',
  shadowCard:    'rgba(0, 0, 0, 0.5)',
  shadowFloat:   'rgba(0, 0, 0, 0.6)',
  shadowOverlay: 'rgba(0, 0, 0, 0.7)',
  tealGlow:      'rgba(32, 184, 205, 0.25)',
} as const;

export type PplxColor = keyof typeof colors;
```

## 2. Typography

FK Grotesk is proprietary; Inter is open. Bundle FK via `expo-font`, fall back to Inter for both faces (they share geometric humanist characteristics).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'FKGrotesk-Medium':       require('../assets/fonts/FKGroteskNeue-Medium.ttf'),
    'FKGrotesk-Semibold':     require('../assets/fonts/FKGroteskNeue-Semibold.ttf'),
    'FKGrotesk-Bold':         require('../assets/fonts/FKGroteskNeue-Bold.ttf'),
    'FKGroteskMono-Medium':   require('../assets/fonts/FKGroteskMono-Medium.ttf'),
    'Inter-Regular':          require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':           require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':         require('../assets/fonts/Inter-SemiBold.ttf'),
    'JetBrainsMono-Regular':  require('../assets/fonts/JetBrainsMono-Regular.ttf'),
    'JetBrainsMono-Medium':   require('../assets/fonts/JetBrainsMono-Medium.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
import { colors } from './colors';

export const typography = {
  // Display & chrome (FK Grotesk)
  display:      { color: colors.textPrimary,   fontFamily: 'FKGrotesk-Bold',     fontSize: 30, lineHeight: 35, letterSpacing: -0.5 },
  question:     { color: colors.textPrimary,   fontFamily: 'FKGrotesk-Semibold', fontSize: 22, lineHeight: 28, letterSpacing: -0.3 },
  section:      { color: colors.textPrimary,   fontFamily: 'FKGrotesk-Semibold', fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  subsection:   { color: colors.textPrimary,   fontFamily: 'FKGrotesk-Semibold', fontSize: 16, lineHeight: 21 },

  // Body (Inter)
  body:         { color: colors.textPrimary,   fontFamily: 'Inter-Regular',      fontSize: 16, lineHeight: 24 },
  bodyBold:     { color: colors.textPrimary,   fontFamily: 'Inter-SemiBold',     fontSize: 16, lineHeight: 24 },
  list:         { color: colors.textPrimary,   fontFamily: 'Inter-Regular',      fontSize: 16, lineHeight: 24 },
  bodySmall:    { color: colors.textPrimary,   fontFamily: 'Inter-Regular',      fontSize: 14, lineHeight: 20 },
  meta:         { color: colors.textSecondary, fontFamily: 'Inter-Regular',      fontSize: 12, lineHeight: 16 },
  caption:      { color: colors.textTertiary,  fontFamily: 'Inter-Regular',      fontSize: 11, lineHeight: 14 },

  // Source cards
  sourceTitle:  { color: colors.textPrimary,   fontFamily: 'Inter-Medium',       fontSize: 13, lineHeight: 17 },
  sourceDomain: { color: colors.textSecondary, fontFamily: 'Inter-Regular',      fontSize: 11, lineHeight: 14 },

  // Citation chip (monospace for alignment)
  citation:     { color: colors.textSecondary, fontFamily: 'FKGroteskMono-Medium', fontSize: 11, lineHeight: 14 },

  // Buttons & chrome
  button:       { color: colors.canvas,        fontFamily: 'FKGrotesk-Semibold', fontSize: 15, lineHeight: 19 },
  proBadge:     { color: colors.canvas,        fontFamily: 'FKGrotesk-Bold',     fontSize: 11, lineHeight: 14, letterSpacing: 0.2 },
  chip:         { color: colors.textPrimary,   fontFamily: 'FKGrotesk-Medium',   fontSize: 13, lineHeight: 17 },
  tab:          { color: colors.textTertiary,  fontFamily: 'FKGrotesk-Medium',   fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
  senderLabel:  { color: colors.textSecondary, fontFamily: 'FKGrotesk-Medium',   fontSize: 13, lineHeight: 17 },
  groupHeader:  { color: colors.textTertiary,  fontFamily: 'FKGrotesk-Bold',     fontSize: 11, lineHeight: 14, letterSpacing: 0.4 },

  // Code
  codeBlock:    { color: colors.codeFg,        fontFamily: 'JetBrainsMono-Regular', fontSize: 14, lineHeight: 21 },
  codeInline:   { color: colors.textPrimary,   fontFamily: 'JetBrainsMono-Medium',  fontSize: 14, lineHeight: 20 },
  codeLang:     { color: colors.textTertiary,  fontFamily: 'JetBrainsMono-Regular', fontSize: 11, lineHeight: 14 },

  // Search input placeholder
  searchPlaceholder: { color: colors.textTertiary, fontFamily: 'Inter-Regular', fontSize: 16, lineHeight: 22 },
} satisfies Record<string, TextStyle>;
```

## 3. The Triple-Circle Brand Mark (SVG)

```tsx
// components/PerplexityMark.tsx
import Svg, { Circle } from 'react-native-svg';
import { colors } from '../theme/colors';

export function PerplexityMark({ size = 20, color = colors.teal }: { size?: number; color?: string }) {
  const c = size / 2;
  const r = size * 0.16;
  const d = r * 1.3;

  // Three circles in a triangular formation
  const angles = [-Math.PI / 2, Math.PI / 6, (5 * Math.PI) / 6];
  return (
    <Svg width={size} height={size}>
      {angles.map((a, i) => (
        <Circle
          key={i}
          cx={c + d * Math.cos(a)}
          cy={c + d * Math.sin(a)}
          r={r}
          fill={color}
        />
      ))}
    </Svg>
  );
}
```

## 4. Signature Components

### Search Input

```tsx
// components/SearchInput.tsx
import { useState } from 'react';
import { View, TextInput, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SearchInput({ onSubmit, onAttach }: { onSubmit: (q: string) => void; onAttach: () => void }) {
  const [text, setText] = useState('');
  const [focused, setFocused] = useState(false);
  const canSend = text.trim().length > 0;

  return (
    <View
      style={{
        marginHorizontal: 16,
        backgroundColor: colors.surface1,
        borderRadius: 24,
        borderWidth: focused ? 1.5 : 1,
        borderColor: focused ? colors.teal : colors.surface3,
        // Teal focus glow
        shadowColor: focused ? colors.teal : 'transparent',
        shadowOpacity: focused ? 0.25 : 0,
        shadowRadius: 4,
        shadowOffset: { width: 0, height: 0 },
        elevation: focused ? 4 : 0,
      }}
    >
      <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 8, paddingHorizontal: 18 }}>
        <TextInput
          value={text}
          onChangeText={setText}
          placeholder="Ask anything…"
          placeholderTextColor={colors.textTertiary}
          multiline
          onFocus={() => setFocused(true)}
          onBlur={() => setFocused(false)}
          style={{ ...typography.body, flex: 1, minHeight: 28, maxHeight: 160, paddingVertical: 14 }}
        />

        <Pressable onPress={onAttach} style={{ paddingBottom: 14 }}>
          <Ionicons name="attach" size={20} color={colors.textSecondary} />
        </Pressable>

        <Pressable
          onPress={() => {
            if (!canSend) return;
            Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
            onSubmit(text);
            setText('');
          }}
          disabled={!canSend}
          style={({ pressed }) => ({
            width: 36, height: 36, borderRadius: 18,
            backgroundColor: canSend ? colors.teal : colors.surface3,
            alignItems: 'center', justifyContent: 'center',
            marginBottom: 10,
            transform: [{ scale: pressed ? 0.94 : 1 }],
          })}
        >
          <Ionicons name="arrow-up" size={14} color={canSend ? colors.canvas : colors.textTertiary} />
        </Pressable>
      </View>
    </View>
  );
}
```

### Source Card

```tsx
// components/SourceCard.tsx
import { View, Text, Image, Pressable } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  number: number;
  domain: string;
  title: string;
  faviconUri?: string;
  onPress: () => void;
};

export function SourceCard({ number, domain, title, faviconUri, onPress }: Props) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        width: 200, height: 80, padding: 12,
        backgroundColor: pressed ? colors.surface2 : colors.surface1,
        borderRadius: 12, borderWidth: 1, borderColor: colors.surface3,
        justifyContent: 'space-between',
      })}
    >
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        {faviconUri ? (
          <Image source={{ uri: faviconUri }} style={{ width: 16, height: 16, borderRadius: 3 }} />
        ) : (
          <View style={{ width: 16, height: 16, borderRadius: 3, backgroundColor: colors.surface3 }} />
        )}
        <Text style={typography.sourceDomain} numberOfLines={1}>{domain}</Text>
        <View style={{ flex: 1 }} />
        <View style={{ paddingHorizontal: 5, paddingVertical: 1, backgroundColor: colors.surface2, borderRadius: 3 }}>
          <Text style={{ ...typography.citation, color: colors.textSecondary, fontWeight: '700' }}>{number}</Text>
        </View>
      </View>

      <Text style={typography.sourceTitle} numberOfLines={2}>{title}</Text>
    </Pressable>
  );
}
```

### Citation Chip (inline `[1]`)

```tsx
// components/CitationChip.tsx
import { useState } from 'react';
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CitationChip({ number, onPress }: { number: number; onPress: () => void }) {
  const [focused, setFocused] = useState(false);

  const handle = () => {
    setFocused(true);
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
    onPress();
    setTimeout(() => setFocused(false), 300);
  };

  return (
    <Pressable onPress={handle} hitSlop={8}>
      <Text
        style={{
          ...typography.citation,
          color: focused ? colors.teal : colors.textSecondary,
          backgroundColor: focused ? colors.tealSoft : colors.surface2,
          borderColor: focused ? colors.teal : colors.surface3,
          borderWidth: 1,
          borderRadius: 4,
          paddingHorizontal: 5,
          lineHeight: 16,
          overflow: 'hidden',
        }}
      >
        {number}
      </Text>
    </Pressable>
  );
}
```

### Answer Block

```tsx
// components/AnswerBlock.tsx
import { View, Text, Pressable } from 'react-native';
import Markdown from 'react-native-markdown-display';
import Ionicons from '@expo/vector-icons/Ionicons';
import { PerplexityMark } from './PerplexityMark';
import { StreamingCursor } from './StreamingCursor';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  content: string;     // raw markdown
  isStreaming: boolean;
  onCopy: () => void;
  onShare: () => void;
  onSave: () => void;
};

export function AnswerBlock({ content, isStreaming, onCopy, onShare, onSave }: Props) {
  return (
    <View style={{ paddingHorizontal: 4, gap: 12 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
        <PerplexityMark size={18} />
        <Text style={typography.senderLabel}>Answer</Text>
      </View>

      <Markdown
        style={{
          body:       typography.body,
          heading1:   typography.section,
          heading2:   typography.subsection,
          em:         { ...typography.body, fontStyle: 'italic' },
          strong:     typography.bodyBold,
          bullet_list_icon: { color: colors.textPrimary },
          code_inline: { ...typography.codeInline, backgroundColor: colors.surface2, paddingHorizontal: 4, borderRadius: 3 },
        }}
      >
        {content}
      </Markdown>

      {isStreaming && <StreamingCursor />}

      <View style={{ flexDirection: 'row', gap: 8, marginTop: 8 }}>
        <ActionPill icon="copy" label="Copy" onPress={onCopy} />
        <ActionPill icon="share-outline" label="Share" onPress={onShare} />
        <ActionPill icon="bookmark-outline" label="Save" onPress={onSave} />
      </View>
    </View>
  );
}

function ActionPill({
  icon, label, onPress,
}: { icon: keyof typeof Ionicons.glyphMap; label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 6,
        paddingVertical: 8, paddingHorizontal: 14,
        backgroundColor: pressed ? colors.surface3 : colors.surface2,
        borderRadius: 8, borderWidth: 1, borderColor: colors.surface3,
      })}
    >
      <Ionicons name={icon} size={13} color={colors.textSecondary} />
      <Text style={typography.chip}>{label}</Text>
    </Pressable>
  );
}
```

### Streaming Cursor

```tsx
// components/StreamingCursor.tsx
import { useEffect } from 'react';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';

export function StreamingCursor() {
  const opacity = useSharedValue(1);

  useEffect(() => {
    opacity.value = withRepeat(withTiming(0, { duration: 250 }), -1, true);
  }, []);

  const style = useAnimatedStyle(() => ({ opacity: opacity.value }));

  return (
    <Animated.View
      style={[
        { width: 6, height: 16, borderRadius: 1, backgroundColor: colors.tealBright },
        style,
      ]}
      accessibilityLabel="Generating answer"
    />
  );
}
```

### Pro Search Toggle

```tsx
// components/ProSearchToggle.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import Animated, { useAnimatedStyle, withSpring } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ProSearchToggle({ isOn, onToggle }: { isOn: boolean; onToggle: () => void }) {
  return (
    <Pressable
      onPress={() => {
        Haptics.selectionAsync();
        onToggle();
      }}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 6,
        alignSelf: 'flex-start',
        paddingVertical: 6, paddingHorizontal: 14,
        backgroundColor: isOn ? colors.teal : colors.surface2,
        borderRadius: 500,
        borderWidth: 1,
        borderColor: isOn ? 'transparent' : colors.surface3,
      }}
    >
      <Ionicons name="sparkles" size={11} color={isOn ? colors.canvas : colors.textSecondary} />
      <Text style={{ ...typography.proBadge, color: isOn ? colors.canvas : colors.textSecondary }}>Pro</Text>
    </Pressable>
  );
}
```

### Searching Indicator

```tsx
// components/SearchingIndicator.tsx
import { useEffect } from 'react';
import { View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withSequence, withTiming, withDelay } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SearchingIndicator() {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, paddingVertical: 12 }}>
      <View style={{ flexDirection: 'row', gap: 6 }}>
        {[0, 1, 2].map((i) => <Dot key={i} delay={i * 150} />)}
      </View>
      <Text style={typography.senderLabel}>Searching the web…</Text>
    </View>
  );
}

function Dot({ delay }: { delay: number }) {
  const scale = useSharedValue(1);

  useEffect(() => {
    scale.value = withDelay(
      delay,
      withRepeat(
        withSequence(
          withTiming(1.3, { duration: 400 }),
          withTiming(1.0, { duration: 400 }),
        ),
        -1,
        true,
      ),
    );
  }, []);

  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Animated.View style={[
      { width: 6, height: 6, borderRadius: 3, backgroundColor: colors.teal },
      style,
    ]} />
  );
}
```

### Pro Steps Card

```tsx
// components/ProStepsCard.tsx
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ProStepsCard({ steps }: { steps: string[] }) {
  const [expanded, setExpanded] = useState(false);

  return (
    <View style={{ backgroundColor: colors.tealSoft, borderRadius: 12, padding: 16, marginHorizontal: 16, marginBottom: 16 }}>
      <Pressable onPress={() => setExpanded((v) => !v)} style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <Ionicons name="sparkles" size={14} color={colors.teal} />
        <Text style={[typography.senderLabel, { color: colors.textPrimary }]}>Steps</Text>
        <Text style={typography.meta}>({steps.length})</Text>
        <View style={{ flex: 1 }} />
        <Ionicons name={expanded ? 'chevron-up' : 'chevron-down'} size={12} color={colors.textSecondary} />
      </Pressable>

      {expanded && (
        <View style={{ gap: 10, marginTop: 12 }}>
          {steps.map((s, i) => (
            <View key={i} style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
              <Ionicons name="checkmark-circle" size={14} color={colors.teal} />
              <Text style={typography.bodySmall}>{s}</Text>
            </View>
          ))}
        </View>
      )}
    </View>
  );
}
```

### Related Questions Card

```tsx
// components/RelatedQuestions.tsx
import { View, Text, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RelatedQuestions({ questions, onSelect }: { questions: string[]; onSelect: (q: string) => void }) {
  return (
    <View style={{ paddingHorizontal: 4, paddingVertical: 16 }}>
      <Text style={[typography.senderLabel, { paddingHorizontal: 16, marginBottom: 8 }]}>Related</Text>
      {questions.map((q, i) => (
        <Pressable
          key={i}
          onPress={() => { Haptics.selectionAsync(); onSelect(q); }}
          style={({ pressed }) => ({
            paddingVertical: 14, paddingHorizontal: 16,
            backgroundColor: pressed ? colors.surface2 : 'transparent',
            borderBottomWidth: i < questions.length - 1 ? 1 : 0,
            borderBottomColor: colors.surface3,
          })}
        >
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
            <Text style={[typography.bodySmall, { flex: 1 }]} numberOfLines={1}>{q}</Text>
            <Ionicons name="arrow-forward" size={14} color={colors.textSecondary} />
          </View>
        </Pressable>
      ))}
    </View>
  );
}
```

## 5. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { View } from 'react-native';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={({ route }) => ({
        tabBarActiveTintColor:   colors.textPrimary,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: colors.canvas,
          borderTopWidth: 0.5, borderTopColor: colors.surface3,
          height: 56,
        },
        tabBarLabelStyle: { fontFamily: 'FKGrotesk-Medium', fontSize: 10, letterSpacing: 0.2 },
        tabBarIcon: ({ focused, color }) => {
          const name = ({
            index:    'search-outline',
            discover: 'compass-outline',
            library:  'library-outline',
            spaces:   'albums-outline',
          } as const)[route.name as 'index'] ?? 'ellipse';
          return (
            <View style={{ alignItems: 'center' }}>
              <Ionicons name={name as any} size={22} color={color} />
              {focused && (
                <View style={{ width: 4, height: 4, borderRadius: 2, backgroundColor: colors.teal, marginTop: 2 }} />
              )}
            </View>
          );
        },
      })}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home' }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover' }} />
      <Tabs.Screen name="library"  options={{ title: 'Library' }} />
      <Tabs.Screen name="spaces"   options={{ title: 'Spaces' }} />
    </Tabs>
  );
}
```

## 6. Motion & Haptics

```tsx
// Send tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Citation chip tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Pro toggle
Haptics.selectionAsync();

// Related question tap
Haptics.selectionAsync();

// Tab switch
Haptics.selectionAsync();

// Streaming cursor blink (Reanimated)
opacity.value = withRepeat(withTiming(0, { duration: 250 }), -1, true);

// Source card cascade-in
cardOpacity.value = withDelay(idx * 60, withTiming(1, { duration: 350 }));

// Pro Steps expand
// Use LayoutAnimation.configureNext({ duration: 300, update: { type: 'easeOut' } });
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons by default).

| Purpose | Ionicons |
|---------|----------|
| Send (arrow up) | `arrow-up` |
| Attach (paperclip) | `attach` |
| Copy | `copy` |
| Share | `share-outline` |
| Save | `bookmark-outline` / `bookmark` |
| Pro sparkles | `sparkles` |
| Step check | `checkmark-circle` |
| Chevron up/down | `chevron-up` / `chevron-down` |
| Related arrow | `arrow-forward` |
| Home tab | `search-outline` |
| Discover tab | `compass-outline` |
| Library tab | `library-outline` |
| Spaces tab | `albums-outline` |
| Sidebar / hamburger | `menu` |
| Overflow | `ellipsis-horizontal` |
| Settings | `settings-outline` |
| Back | `chevron-back` |
| Show all sources | `chevron-forward` |

## 8. Platform Notes

- **Dark-first**: Perplexity defaults to dark. Detect light mode via `useColorScheme()` and override token bindings if needed; the brand can run light but most users see dark.
- **Status bar**: Set `<StatusBar style="light" />` on the dark canvas; `"dark"` on light mode.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The search input pins above the keyboard via `KeyboardAvoidingView` (`behavior="padding"` on iOS).
- **Search input auto-grow**: Use `multiline` on `TextInput` with min-height 28pt and max-height 160pt; switches to internal scroll past the max.
- **Markdown rendering**: Use `react-native-markdown-display` and override the style map with the `typography` tokens. Citation references in the source markdown (e.g., `[1]`) should be parsed via a custom AST renderer that swaps the text for a `<CitationChip />` component.
- **Citation chip → source card scroll**: Use a `ScrollView`'s `scrollTo` ref-method to animate to the source card row. Highlight the target card with a 200ms teal border flash on arrival (animate `borderColor`).
- **Streaming text**: Animate word-by-word, buffering tokens until a whitespace boundary. Use 60ms fade-in per word (slightly faster than Claude's 80ms — Perplexity's prose is denser).
- **Source card cascade**: On result return, animate each `SourceCard` in with a 60ms stagger using Reanimated `withDelay(idx * 60, withTiming(1, ...))`.
- **Dynamic Type**: React Native respects user font-scaling. Set `allowFontScaling={false}` on citation chips, source domain text, tab labels, code-block language label, and the Pro badge — everywhere else, scale.
- **Accessibility**: Add `accessibilityRole="button"` + descriptive `accessibilityLabel` on the search input ("Search field, ask anything"), citation chips ("Citation 1, opens source 1"), source cards ("Source 1, wikipedia.org, Bayesian Inference"). The streaming cursor has `accessibilityLabel="Generating answer"`. Announce "Answer complete" via `AccessibilityInfo.announceForAccessibility` when streaming ends.
- **Reduce Motion**: Check `AccessibilityInfo.isReduceMotionEnabled()` and disable the streaming cursor blink, the searching-dot pulse, the source card cascade, and the focus-ring glow — preserve haptics.
- **iPad two-column layout**: Use `useWindowDimensions()` to detect tablet width (>= 768pt). When ≥ 1024pt, render answer (60% width) and source list (40% sidebar) side-by-side; below that, source list stays as horizontal row above the answer.
- **Performance**: Use `FlatList` (not `ScrollView`) for thread history and discover feed. Memoize `SourceCard` and `AnswerBlock` keyed on their identifiers and streaming state.
