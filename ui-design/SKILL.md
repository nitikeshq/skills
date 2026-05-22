---
name: designer
description: >
  Master design skill for production-grade web and mobile UI. Triggers on ANY design task:
  building pages, components, dashboards, landing pages, mobile apps, admin panels, SaaS products,
  e-commerce, portfolios, or any visual UI work. Enforces correct aesthetic selection
  (glassmorphism, neobrutalism, neumorphism, claymorphism, bento, minimalism, etc.), proper
  branding with color tokens and font systems, mandatory mobile responsiveness for web,
  platform-correct patterns for native mobile, and full accessibility compliance. Use when the
  user says "build", "design", "create", "make", "style", "improve", "fix the UI", "make it look
  better", or any phrase implying visual output. Also use for design reviews, UX audits,
  component creation, dark mode, animations, forms, navigation, and data visualization.
  Never produce generic AI aesthetics — no Inter/Roboto/purple-on-white defaults.
---

# Designer — Master Design Skill

Production-grade UI/UX for web and mobile. Enforces aesthetic intentionality, correct branding, responsive layout, platform-correct patterns, and accessibility from the first line of code.

## Design Intelligence Flow

Follow this order on every task:

```
1. UNDERSTAND  → Platform, product type, brand context
2. SELECT      → Aesthetic + palette + typography system
3. CONSTRAIN   → Web responsiveness OR mobile platform rules
4. BUILD       → Code with full design system applied
5. VERIFY      → Pre-delivery checklist
```

Never skip step 1–2. Never write a single CSS class before committing to an aesthetic direction.

---

## Step 1: Understand the Context

Extract these before designing:

| Dimension | Questions to Answer |
|---|---|
| **Platform** | Web (responsive)? iOS native? Android native? React Native? Flutter? Both? |
| **Product type** | SaaS / dashboard / landing / e-commerce / portfolio / mobile app / admin |
| **Brand state** | Has existing brand (colors/fonts/logo)? Starting fresh? |
| **Audience** | Consumer / enterprise / developer / general public |
| **Tone** | Playful / minimal / luxury / bold / editorial / corporate / dark / vibrant |
| **Stack** | HTML+CSS / React / Next.js / Vue / React Native / Flutter / SwiftUI |

If brand colors/fonts already exist → extract and honour them. If none → go to Step 2 and generate.

---

## Step 2: Aesthetic Selection

### 2A. Choose the Right Aesthetic

Match aesthetic to product + tone. Never mix randomly.

| Aesthetic | Best For | Avoid When |
|---|---|---|
| **Glassmorphism** | Premium SaaS, fintech, dark-first dashboards, AI products | Government, healthcare, accessibility-critical |
| **Neumorphism** | Productivity apps, music players, settings UIs, calm tools | Complex data-heavy UIs, small elements, dark backgrounds |
| **Neobrutalism** | Creative agencies, developer tools, startups, bold portfolios | Healthcare, finance, conservative enterprise |
| **Claymorphism** | Consumer apps, games, children's products, food/lifestyle | Enterprise, B2B, data-heavy products |
| **Bento Grid** | Portfolios, feature showcases, modern SaaS marketing | Mobile-first (becomes unwieldy), simple landing pages |
| **Dark Minimalism** | Developer tools, crypto, AI products, night-mode apps | Healthcare, children's products, high-accessibility needs |
| **Editorial/Magazine** | Blogs, media, news, content platforms | Dashboards, admin panels, utility apps |
| **Luxury Refinement** | Fashion, hospitality, high-end e-commerce, real estate | Consumer utilities, budget products |
| **Corporate Flat** | Enterprise software, HR tools, government portals | Consumer apps, creative industries |

**Commit to ONE primary aesthetic per product.** Accent effects (subtle glass on a neobrutalist card) are allowed; wholesale mixing is not.

### 2B. Aesthetic Implementation Specs

#### Glassmorphism
```css
/* Card */
background: rgba(255, 255, 255, 0.08);
backdrop-filter: blur(20px) saturate(180%);
-webkit-backdrop-filter: blur(20px) saturate(180%);
border: 1px solid rgba(255, 255, 255, 0.15);
border-radius: 16px;
box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);

/* RULES:
   - Needs a rich background (gradient mesh, blurred imagery, dark gradient)
   - Blur radius: 12–24px for cards, 40–60px for overlays
   - White border at 10–20% opacity only
   - Works best dark-first; avoid on white backgrounds
   - Layer depth: background → glass card → content (3 layers minimum)
*/
```

#### Neumorphism
```css
/* Light mode surface */
background: #e0e5ec;
border-radius: 16px;
box-shadow:
  6px 6px 12px rgba(163, 177, 198, 0.6),
  -6px -6px 12px rgba(255, 255, 255, 0.8);

/* Pressed/inset state */
box-shadow:
  inset 4px 4px 8px rgba(163, 177, 198, 0.6),
  inset -4px -4px 8px rgba(255, 255, 255, 0.8);

/* RULES:
   - Background, card, and shadow must all be tints/shades of the SAME base hue
   - Never use on black/very dark backgrounds — defeats the soft-shadow effect
   - Minimum contrast ratio still must hit 4.5:1 for text
   - Avoid tiny elements (<32px) — shadow detail is lost
   - One shadow colour = darker tint, other = lighter tint of base
*/
```

#### Neobrutalism
```css
/* Card */
background: #fff;
border: 2.5px solid #000;
border-radius: 4px; /* minimal radius — intentionally blunt */
box-shadow: 4px 4px 0px #000;

/* Button */
background: #FFE500; /* bold, saturated fill */
border: 2.5px solid #000;
box-shadow: 3px 3px 0px #000;
transition: transform 0.1s, box-shadow 0.1s;
/* hover: */ transform: translate(-2px, -2px); box-shadow: 5px 5px 0px #000;
/* active: */ transform: translate(2px, 2px); box-shadow: 0px 0px 0px #000;

/* RULES:
   - Black borders EVERYWHERE — cards, buttons, inputs, images
   - Flat hard-offset shadows (no blur: 0)
   - Accent colours: 1–2 bold, fully saturated fills
   - Typography: heavy weights (700–900), oversized headings
   - No border-radius beyond 4–8px
   - Intentional white space — don't cram
*/
```

#### Claymorphism
```css
/* Card */
background: linear-gradient(145deg, #ff9a9e, #fecfef);
border-radius: 24px;
box-shadow:
  inset 0 -4px 8px rgba(0,0,0,0.15),
  inset 0 4px 8px rgba(255,255,255,0.4),
  0 12px 32px rgba(0,0,0,0.15);

/* RULES:
   - Thick inner shadow gives the 3D clay illusion
   - Always rounded: 20–32px border-radius
   - Soft gradients within elements (not flat fills)
   - Colours: pastel/candy — no dark, no muted
*/
```

---

## Step 3A: Color System (Branding)

### If brand colors exist → Extract and tokenize:
```css
:root {
  --brand-primary: /* extracted */;
  --brand-secondary: /* extracted */;
  --brand-accent: /* extracted */;
  --surface-bg: /* dark or light base */;
  --surface-card: /* card background */;
  --surface-border: /* border color */;
  --text-primary: /* highest contrast text */;
  --text-secondary: /* reduced emphasis text */;
  --text-muted: /* captions, placeholders */;
  --interactive-hover: /* hover state */;
  --interactive-active: /* pressed state */;
  --state-error: #ef4444;
  --state-success: #22c55e;
  --state-warning: #f59e0b;
  --state-info: #3b82f6;
}
```

### If no brand → Select palette by product type:

| Product Type | Palette Direction | Example Hex |
|---|---|---|
| SaaS / Tech | Deep navy + electric blue + white | `#0F172A` + `#3B82F6` + `#F8FAFC` |
| Fintech / Banking | Deep teal + gold + near-white | `#0D4A4A` + `#D4AF37` + `#F5F5F0` |
| Healthcare | Clean white + medical blue + sage | `#FFFFFF` + `#2563EB` + `#6B8F71` |
| E-commerce | Deep charcoal + vivid accent + off-white | `#1C1C1E` + `#FF6B35` + `#FAFAF8` |
| Creative/Agency | Black + one dominant bold colour | `#000000` + `#FF3366` + `#FFFFFF` |
| Food/Lifestyle | Warm whites + earthy tones + pops | `#FDF6EC` + `#C85A2A` + `#2D5016` |
| Real Estate | Muted slate + warm gold + cream | `#334155` + `#B8966A` + `#FBF9F6` |
| Developer Tools | Near-black + neon accent + grey | `#0A0A0A` + `#00FF88` + `#2D2D2D` |
| Education | White + optimistic blue + yellow | `#FFFFFF` + `#1D4ED8` + `#FCD34D` |
| Government/NGO | White + institutional blue/green | `#FFFFFF` + `#1E40AF` + `#166534` |

**Color rules:**
- Always define in CSS variables or design tokens — never raw hex in components
- Pick 1 dominant (60%) + 1 supporting (30%) + 1 accent (10%)
- Test contrast: body text ≥ 4.5:1, large text/icons ≥ 3:1 (WCAG AA)
- Verify BOTH light and dark mode independently
- Functional colors (error/success/warning) are never brand colors — keep them semantic red/green/amber

---

## Step 3B: Typography System

### BANNED fonts (generic AI defaults — never use):
`Inter`, `Roboto`, `Arial`, `Helvetica`, `system-ui` as primary display font, `Space Grotesk` (overused)

### Choose a distinctive pairing:

| Aesthetic | Display Font | Body Font | Mono (if needed) |
|---|---|---|---|
| Luxury/Editorial | Playfair Display, Cormorant Garamond, Italiana | Lora, Libre Baskerville | IBM Plex Mono |
| Bold/Neobrutalist | Bebas Neue, Archivo Black, Big Shoulders Display | Work Sans, DM Sans | JetBrains Mono |
| Modern SaaS | Bricolage Grotesque, Outfit, Sora | Instrument Sans, DM Sans | Geist Mono |
| Dark/Technical | Jura, Tektur, Oxanium | Work Sans, Outfit | JetBrains Mono |
| Playful/Consumer | Nunito, Quicksand, Fredoka | Nunito, Poppins | DM Mono |
| Minimalist | Young Serif, Gloock, Libre Baskerville | Instrument Sans | Geist Mono |
| Developer/Code | JetBrains Mono, Geist Mono | Work Sans | — |

### Font scale (apply consistently):
```css
:root {
  --text-xs:   0.75rem;   /* 12px — captions, labels */
  --text-sm:   0.875rem;  /* 14px — secondary body */
  --text-base: 1rem;      /* 16px — primary body (minimum on mobile) */
  --text-lg:   1.125rem;  /* 18px — large body */
  --text-xl:   1.25rem;   /* 20px — small headings */
  --text-2xl:  1.5rem;    /* 24px */
  --text-3xl:  1.875rem;  /* 30px */
  --text-4xl:  2.25rem;   /* 36px */
  --text-5xl:  3rem;      /* 48px — hero headings */
  --text-6xl:  3.75rem;   /* 60px — display */

  --leading-tight:  1.25;
  --leading-snug:   1.375;
  --leading-normal: 1.5;
  --leading-relaxed:1.625;

  --weight-regular: 400;
  --weight-medium:  500;
  --weight-semibold:600;
  --weight-bold:    700;
  --weight-black:   900;
}
```

**Rules:**
- Body text: minimum 16px on mobile (prevents iOS auto-zoom)
- Line height: 1.5–1.625 for body, 1.1–1.25 for large headings
- Line length: 35–60 chars mobile, 60–75 chars desktop
- Use tabular-nums for prices, data, counters

---

## Step 4A: Web Responsiveness (MANDATORY for all web)

### Breakpoint system — always use these:
```css
/* Mobile first — build smallest screen first, then scale up */
/* xs: 0–374px   (small phones) */
/* sm: 375px     (standard mobile — design target) */
/* md: 768px     (tablet) */
/* lg: 1024px    (small desktop) */
/* xl: 1280px    (desktop) */
/* 2xl: 1440px   (wide desktop) */

/* In Tailwind: sm: md: lg: xl: 2xl: */
/* In CSS: */
@media (min-width: 768px) { /* tablet+ */ }
@media (min-width: 1024px) { /* desktop+ */ }
@media (min-width: 1440px) { /* wide */ }
```

### Viewport meta — required in every HTML document:
```html
<meta name="viewport" content="width=device-width, initial-scale=1">
<!-- NEVER: user-scalable=no — accessibility violation -->
```

### Responsive layout patterns:

| Component | Mobile | Tablet | Desktop |
|---|---|---|---|
| Navigation | Bottom bar or hamburger | Side rail or top nav | Full top nav or sidebar |
| Cards grid | 1 column | 2 columns | 3–4 columns |
| Hero | Stacked (text above image) | Side-by-side | Full-bleed with overlay |
| Dashboard | Single column, tabs | 2-col | Multi-pane with sidebar |
| Forms | Full width, stacked labels | 60–80% width | 40–60% width centered |
| Data tables | Horizontal scroll or card-list | Full table | Full table + sticky columns |
| Sidebar | Hidden (hamburger/drawer) | Collapsible icon rail | Persistent expanded |

### Container widths:
```css
.container {
  width: 100%;
  padding-inline: 1rem;       /* 16px mobile */
  margin-inline: auto;
}
@media (min-width: 768px)  { .container { padding-inline: 2rem; } }
@media (min-width: 1024px) { .container { max-width: 1024px; padding-inline: 2.5rem; } }
@media (min-width: 1280px) { .container { max-width: 1280px; } }
@media (min-width: 1440px) { .container { max-width: 1400px; } }
```

### Spacing system (8dp grid):
```
4px  → micro gaps (icon to label)
8px  → tight gaps (related elements)
12px → small gaps
16px → base unit (standard padding)
24px → medium gaps (between components)
32px → large gaps (between sections)
48px → section separation
64px → major section breaks
96px → hero/page-level spacing
```

### Z-index scale (always use these — no ad-hoc values):
```css
:root {
  --z-base:    0;
  --z-raised:  10;   /* cards on hover */
  --z-dropdown:20;   /* dropdowns */
  --z-sticky:  40;   /* sticky headers */
  --z-overlay: 60;   /* overlays/backdrops */
  --z-modal:   80;   /* modals/drawers */
  --z-toast:   100;  /* toasts/notifications */
  --z-tooltip: 120;  /* tooltips */
}
```

### What is NEVER acceptable on web:
- Horizontal scroll (except intentional carousels with visible affordance)
- Fixed pixel widths that break on smaller screens
- Touch targets below 44×44px
- `user-scalable=no` in viewport meta
- Text below 12px at any breakpoint
- Content hidden under fixed headers (always add scroll-padding-top)
- Relying on hover as the only interaction affordance (mobile has no hover)

---

## Step 4B: Mobile Native Design (iOS / Android / React Native / Flutter)

### Web ≠ Mobile. These are fundamentally different design contexts.

| Dimension | Web (Responsive) | Native Mobile |
|---|---|---|
| Navigation | Top nav, sidebar, hamburger | Bottom tab bar (iOS) / Top app bar (Android) |
| Touch targets | ≥44px recommended | ≥44pt iOS / ≥48dp Android — MANDATORY |
| Typography | rem/px, viewport-relative | Dynamic Type (iOS) / Material type scale (Android) |
| Gestures | Click + hover | Tap, swipe, long press, pinch |
| Back behavior | Browser back | iOS swipe-from-left / Android predictive back |
| Safe areas | No device chrome concern | Notch, Dynamic Island, home indicator, status bar |
| Scroll | CSS overflow | Platform scroll physics (rubber banding iOS) |
| Modals | CSS dialog/overlay | Native sheets (slide up from bottom) |

### Safe area handling (React Native):
```jsx
import { SafeAreaView, useSafeAreaInsets } from 'react-native-safe-area-context';

// Wrap screens:
<SafeAreaView style={{ flex: 1 }}>...</SafeAreaView>

// For custom bottom bars:
const insets = useSafeAreaInsets();
<View style={{ paddingBottom: insets.bottom + 16 }}>...</View>
```

### iOS-specific rules:
- Bottom tab bar: 5 items max, icon + label
- Navigation: large title style on main screens, inline title in sub-screens
- Modals: sheet presentation (slide from bottom), swipe-down to dismiss
- Haptic feedback: UIImpactFeedbackGenerator for confirmations
- Back navigation: support system swipe-from-left-edge gesture
- Support Dynamic Type: never set a fixed font size — use Text style variants

### Android-specific rules:
- Top App Bar for primary structure, FAB for primary action
- Bottom navigation: 3–5 items, support predictive back gesture
- Material state layers: ripple on all interactive surfaces
- 8dp spacing grid throughout
- Avoid iOS-specific patterns (sheets, large titles) — use Material equivalents

### React Native touch targets:
```jsx
// Extend hit area beyond visual bounds:
<Pressable
  hitSlop={{ top: 10, bottom: 10, left: 10, right: 10 }}
  onPress={handler}
>
  <Icon size={24} />
</Pressable>
```

---

## Step 5: Motion & Animation

### Timing values:
```css
:root {
  --duration-instant:  80ms;   /* immediate feedback */
  --duration-fast:     150ms;  /* micro-interactions */
  --duration-normal:   250ms;  /* standard transitions */
  --duration-slow:     400ms;  /* complex transitions, modals */
  --duration-slowest:  600ms;  /* page-level entrances */

  --ease-out:   cubic-bezier(0, 0, 0.2, 1);  /* entering */
  --ease-in:    cubic-bezier(0.4, 0, 1, 1);  /* exiting */
  --ease-inout: cubic-bezier(0.4, 0, 0.2, 1);/* within-state */
  --ease-spring:cubic-bezier(0.34, 1.56, 0.64, 1); /* spring feel */
}
```

### Animation rules:
- Animate only `transform` and `opacity` — never `width`, `height`, `top`, `left`, `padding`
- Exit animations: 60–70% of enter duration (feels snappy)
- Page load: one orchestrated stagger (30–50ms delay per item) beats scattered micro-interactions
- Always respect `prefers-reduced-motion`:
```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```
- Max 2 animated elements in view at once
- Modals: scale(0.95) + opacity 0 → scale(1) + opacity 1

---

## Step 6: Component Constraints

### Buttons:
```
Primary:   filled brand color, bold label, 44px min height
Secondary: outlined/ghost, same height as primary
Danger:    red fill or red outline, spatially separated from primary
Disabled:  38–50% opacity, cursor: not-allowed, non-interactive
Loading:   spinner inside button + disabled state during async
Size:      Mobile ≥44px height; Web ≥40px height
```

### Cards:
```
Padding:    16px mobile, 20–24px desktop
Radius:     Match aesthetic — 4px neobrutalist, 12–16px modern, 20–24px clay
Shadow:     1 elevation level only per card type — don't mix depths randomly
Hover:      translateY(-2px) + shadow increase on web, scale(0.98) press on mobile
```

### Forms:
```
Label:      Always visible above input — never placeholder-only
Height:     ≥44px all inputs on mobile
Error:      Red text below the specific field (not just top of form)
Required:   Asterisk (*) with SR-only explanation
Helper:     Persistent below input, not just placeholder
Validation: On blur — not on every keystroke
```

### Navigation:
```
Web desktop:   Persistent sidebar OR full top nav (not both)
Web mobile:    Hamburger → drawer, or bottom bar for ≤5 primary items
iOS:           Bottom tab bar (max 5), swipe-back support
Android:       Top app bar + bottom nav or nav drawer
Active state:  Always clearly indicated (color + weight + indicator)
```

---

## Step 7: Accessibility Non-Negotiables

These are NEVER optional:

| Rule | Requirement |
|---|---|
| Color contrast | Body text ≥ 4.5:1, large text/UI ≥ 3:1 (WCAG AA) |
| Viewport meta | Never `user-scalable=no` |
| Images | Meaningful images need `alt` text; decorative get `alt=""` |
| Buttons | Icon-only buttons need `aria-label` |
| Focus rings | Never `outline: none` without a custom visible replacement |
| Heading order | Sequential h1→h6, no skipping levels |
| Color meaning | Never convey information with color alone (add icon or text) |
| Form labels | Every input has an associated `<label>` |
| Touch targets | ≥44×44px on mobile |
| Motion | `prefers-reduced-motion` respected |
| Tab order | Matches visual reading order |
| Error messages | Must state what went wrong AND how to fix it |

---

## Step 8: Pre-Delivery Checklist

Before delivering any design:

**Visual Quality**
- [ ] Aesthetic is named and consistently applied throughout
- [ ] No banned fonts (Inter, Roboto, Arial, Space Grotesk as primary)
- [ ] No raw hex values in components — CSS variables only
- [ ] No emoji used as UI icons
- [ ] Brand colors tokenized and used consistently
- [ ] Dark/light mode both tested (if applicable)

**Responsive (Web)**
- [ ] Viewport meta tag present
- [ ] Tested at 375px, 768px, 1024px, 1440px
- [ ] No horizontal scroll on mobile
- [ ] No content hidden under fixed headers
- [ ] Touch targets ≥44px
- [ ] Navigation pattern changes appropriately between breakpoints
- [ ] Images have defined dimensions (no layout shift)
- [ ] Container max-widths capped and centred on desktop

**Mobile Native**
- [ ] Safe areas respected (notch, home indicator)
- [ ] Touch targets ≥44pt (iOS) / ≥48dp (Android)
- [ ] Platform navigation pattern used correctly
- [ ] System gestures not blocked
- [ ] Dynamic Type or Material type scale used

**Accessibility**
- [ ] Color contrast verified (4.5:1 body, 3:1 UI)
- [ ] All interactive elements keyboard-navigable
- [ ] aria-labels on icon-only buttons
- [ ] Focus rings visible
- [ ] prefers-reduced-motion respected
- [ ] Error messages contain cause + fix

**Design System**
- [ ] Z-index from token scale only
- [ ] Spacing from 8dp grid only
- [ ] Animation using defined duration + easing tokens
- [ ] Type scale from defined system
- [ ] One aesthetic direction — no random mixing

---

## Reference Files

Read these when you need deeper guidance on specific areas:

| File | Read When |
|---|---|
| [`references/aesthetic-recipes.md`](references/aesthetic-recipes.md) | Building a specific aesthetic (full code recipes) |
| [`references/color-palettes.md`](references/color-palettes.md) | Choosing or generating a color system |
| [`references/typography-pairings.md`](references/typography-pairings.md) | Selecting and implementing font pairs |
| [`references/responsive-patterns.md`](references/responsive-patterns.md) | Complex responsive layout patterns |
| [`references/mobile-native.md`](references/mobile-native.md) | iOS / Android / React Native / Flutter specific rules |
| [`references/ux-rules.md`](references/ux-rules.md) | Full 99-rule UX checklist (accessibility, animation, forms, nav, charts) |
| [`references/component-specs.md`](references/component-specs.md) | Button, card, form, modal, nav specs |
