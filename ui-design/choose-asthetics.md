# Aesthetic Recipes

Full implementation recipes for each supported aesthetic. Each includes background, card, button, input, and typography specs.

---

## Glassmorphism

### When to use
Dark-first premium products: SaaS dashboards, fintech, AI tools, crypto, music apps.

### Background (required — glass needs something to blur)
```css
body {
  background:
    radial-gradient(ellipse at 20% 50%, rgba(120, 40, 200, 0.35) 0%, transparent 60%),
    radial-gradient(ellipse at 80% 20%, rgba(20, 120, 255, 0.3) 0%, transparent 60%),
    radial-gradient(ellipse at 60% 80%, rgba(0, 200, 150, 0.2) 0%, transparent 60%),
    #050510;
  min-height: 100vh;
}
/* Add noise texture for depth */
body::before {
  content: '';
  position: fixed;
  inset: 0;
  background-image: url("data:image/svg+xml,..."); /* SVG noise */
  opacity: 0.04;
  pointer-events: none;
  z-index: 0;
}
```

### Card
```css
.glass-card {
  background: rgba(255, 255, 255, 0.07);
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
  border: 1px solid rgba(255, 255, 255, 0.12);
  border-radius: 16px;
  box-shadow:
    0 8px 32px rgba(0, 0, 0, 0.4),
    inset 0 1px 0 rgba(255, 255, 255, 0.15);
  padding: 24px;
}
/* Hover lift */
.glass-card:hover {
  background: rgba(255, 255, 255, 0.10);
  border-color: rgba(255, 255, 255, 0.20);
  transform: translateY(-2px);
  box-shadow: 0 16px 48px rgba(0, 0, 0, 0.5);
  transition: all 250ms cubic-bezier(0, 0, 0.2, 1);
}
```

### Button (primary)
```css
.btn-glass-primary {
  background: linear-gradient(135deg, rgba(99, 102, 241, 0.9), rgba(139, 92, 246, 0.9));
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 10px;
  color: #fff;
  font-weight: 600;
  padding: 12px 24px;
  min-height: 44px;
  backdrop-filter: blur(10px);
  box-shadow: 0 4px 16px rgba(99, 102, 241, 0.4);
  transition: all 150ms ease-out;
}
.btn-glass-primary:hover {
  box-shadow: 0 8px 24px rgba(99, 102, 241, 0.6);
  transform: translateY(-1px);
}
```

### Input
```css
.glass-input {
  background: rgba(255, 255, 255, 0.06);
  border: 1px solid rgba(255, 255, 255, 0.12);
  border-radius: 10px;
  color: #fff;
  padding: 12px 16px;
  min-height: 44px;
  backdrop-filter: blur(8px);
  width: 100%;
  transition: border-color 150ms;
}
.glass-input:focus {
  border-color: rgba(99, 102, 241, 0.6);
  outline: none;
  box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.15);
}
```

### Typography for Glassmorphism
- Display: Sora 700 or Bricolage Grotesque 700
- Body: Outfit 400/500
- Colors: `#F8FAFC` primary, `rgba(248,250,252,0.6)` secondary, `rgba(248,250,252,0.35)` muted

---

## Neumorphism

### When to use
Calm productivity apps, music/audio UIs, settings screens, wellness tools. Light mode only.

### Base surface
```css
:root {
  --neu-bg: #E4E9F2;           /* base hue */
  --neu-shadow-dark: #B8C0CC;  /* darker tint */
  --neu-shadow-light: #FFFFFF; /* lighter tint */
  --neu-radius: 16px;
}
body {
  background: var(--neu-bg);
}
```

### Raised element (card/button at rest)
```css
.neu-raised {
  background: var(--neu-bg);
  border-radius: var(--neu-radius);
  box-shadow:
    6px 6px 14px var(--neu-shadow-dark),
    -6px -6px 14px var(--neu-shadow-light);
  padding: 24px;
}
```

### Pressed/active (inset)
```css
.neu-pressed {
  background: var(--neu-bg);
  border-radius: var(--neu-radius);
  box-shadow:
    inset 4px 4px 10px var(--neu-shadow-dark),
    inset -4px -4px 10px var(--neu-shadow-light);
}
```

### Concave element (input focus)
```css
.neu-input {
  background: var(--neu-bg);
  border-radius: 12px;
  border: none;
  box-shadow:
    inset 3px 3px 8px var(--neu-shadow-dark),
    inset -3px -3px 8px var(--neu-shadow-light);
  padding: 12px 16px;
  min-height: 44px;
  width: 100%;
  outline: none;
  color: #2d3748;
}
```

### Toggle (convex + active states)
```css
.neu-toggle-track {
  background: var(--neu-bg);
  border-radius: 50px;
  box-shadow: inset 3px 3px 7px var(--neu-shadow-dark), inset -3px -3px 7px var(--neu-shadow-light);
  width: 52px; height: 28px;
  position: relative;
}
.neu-toggle-thumb {
  position: absolute;
  top: 4px; left: 4px;
  width: 20px; height: 20px;
  border-radius: 50%;
  background: var(--neu-bg);
  box-shadow: 2px 2px 5px var(--neu-shadow-dark), -2px -2px 5px var(--neu-shadow-light);
  transition: left 200ms ease;
}
.neu-toggle-track.active .neu-toggle-thumb { left: 28px; }
```

### Typography for Neumorphism
- Display: Nunito 700 or Poppins 600
- Body: Nunito 400 or Poppins 400
- Colors: `#2D3748` primary, `#718096` secondary, `#A0AEC0` muted
- Never pure black text on neumorphic surfaces — use `#2D3748` max

### Critical warnings:
- The base background, card, and shadow shades MUST derive from the same hue
- Minimum element size: 32px (shadow detail is lost below this)
- Text contrast STILL must pass 4.5:1 — verify with a tool
- Avoid multiple light sources (one shadow direction throughout)

---

## Neobrutalism

### When to use
Creative agencies, startup landing pages, developer tools, bold portfolios. Statement-making products.

### Palette (high contrast, bold)
```css
:root {
  --neo-bg: #FFFFFF;           /* or #F5F0E8 cream */
  --neo-black: #000000;
  --neo-accent: #FFE500;       /* single bold accent — swap per brand */
  --neo-accent-2: #FF3366;     /* optional second accent, use sparingly */
  --neo-border: 2.5px solid #000;
  --neo-shadow: 4px 4px 0px #000;
  --neo-shadow-hover: 6px 6px 0px #000;
}
```

### Card
```css
.neo-card {
  background: var(--neo-bg);
  border: var(--neo-border);
  border-radius: 4px;
  box-shadow: var(--neo-shadow);
  padding: 24px;
  transition: transform 120ms, box-shadow 120ms;
}
.neo-card:hover {
  transform: translate(-2px, -2px);
  box-shadow: var(--neo-shadow-hover);
}
```

### Button
```css
.btn-neo-primary {
  background: var(--neo-accent);
  border: var(--neo-border);
  border-radius: 4px;
  box-shadow: var(--neo-shadow);
  color: #000;
  font-weight: 700;
  font-size: 0.9rem;
  letter-spacing: 0.02em;
  text-transform: uppercase;
  padding: 12px 24px;
  min-height: 44px;
  cursor: pointer;
  transition: transform 100ms, box-shadow 100ms;
}
.btn-neo-primary:hover {
  transform: translate(-2px, -2px);
  box-shadow: 6px 6px 0px #000;
}
.btn-neo-primary:active {
  transform: translate(2px, 2px);
  box-shadow: 0px 0px 0px #000;
}
.btn-neo-secondary {
  background: transparent;
  border: var(--neo-border);
  box-shadow: var(--neo-shadow);
  /* same hover/active */
}
```

### Input
```css
.neo-input {
  background: #fff;
  border: var(--neo-border);
  border-radius: 4px;
  box-shadow: 2px 2px 0px #000;
  padding: 12px 16px;
  min-height: 44px;
  width: 100%;
  font-weight: 500;
  transition: box-shadow 100ms;
}
.neo-input:focus {
  outline: none;
  box-shadow: 4px 4px 0px var(--neo-accent);
}
```

### Typography for Neobrutalism
- Display: Archivo Black 900 / Bebas Neue / Big Shoulders Display 800
- Body: Work Sans 400/500 or DM Sans
- Sizes: massive headings — don't be shy (4rem–8rem on desktop)
- Letter-spacing: -0.02em to -0.04em on display text
- All-caps labels and button text: `text-transform: uppercase; letter-spacing: 0.08em`

### Image treatment
```css
.neo-image {
  border: var(--neo-border);
  box-shadow: var(--neo-shadow);
  /* all images get the black border + shadow */
}
```

---

## Claymorphism

### When to use
Consumer apps, onboarding, gamification, lifestyle/food, children's products.

### Card
```css
.clay-card {
  background: linear-gradient(145deg, #a78bfa, #818cf8);
  border-radius: 24px;
  box-shadow:
    inset 0 -5px 10px rgba(0, 0, 0, 0.2),
    inset 0 5px 10px rgba(255, 255, 255, 0.35),
    0 15px 35px rgba(99, 102, 241, 0.35),
    0 5px 15px rgba(0, 0, 0, 0.1);
  padding: 24px;
}
```

### Palette (clay always uses candy/pastel)
```
Coral:    #FF6B6B → #FF8E8E gradient
Mint:     #6BCB77 → #8CE8A2 gradient
Lilac:    #A78BFA → #C4B5FD gradient
Peach:    #FDA4AF → #FCA5A5 gradient
Sky:      #7DD3FC → #BAE6FD gradient
Butter:   #FDE68A → #FEF08A gradient
```

### Button
```css
.btn-clay {
  background: linear-gradient(145deg, #a78bfa, #7c3aed);
  border-radius: 16px;
  border: none;
  box-shadow:
    inset 0 -3px 6px rgba(0,0,0,0.2),
    inset 0 3px 6px rgba(255,255,255,0.3),
    0 8px 20px rgba(124, 58, 237, 0.4);
  color: #fff;
  font-weight: 700;
  padding: 14px 28px;
  min-height: 48px;
  transition: transform 150ms, box-shadow 150ms;
}
.btn-clay:hover { transform: translateY(-2px); }
.btn-clay:active {
  transform: translateY(1px);
  box-shadow:
    inset 0 -1px 3px rgba(0,0,0,0.2),
    inset 0 1px 3px rgba(255,255,255,0.3),
    0 4px 10px rgba(124, 58, 237, 0.3);
}
```

### Typography for Claymorphism
- Display: Nunito 800 / Fredoka One / Quicksand 700
- Body: Nunito 500 / Poppins 400
- Colors: High contrast on coloured surfaces — use dark navy `#1e1b4b` or white

---

## Bento Grid

### When to use
Portfolio showcases, feature highlights on SaaS marketing pages, product showcase sections.

### Layout
```css
.bento-grid {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  gap: 16px;
  padding: 16px;
}
/* Cells span different widths for visual rhythm */
.bento-cell-wide   { grid-column: span 8; }
.bento-cell-narrow { grid-column: span 4; }
.bento-cell-half   { grid-column: span 6; }
.bento-cell-full   { grid-column: span 12; }

/* On mobile — collapse to single column */
@media (max-width: 767px) {
  .bento-grid > * { grid-column: span 12 !important; }
}
```

### Cell
```css
.bento-cell {
  background: #fff;
  border: 1px solid #e2e8f0;
  border-radius: 20px;
  padding: 28px;
  overflow: hidden;
  position: relative;
  /* cells can be glassmorphism, solid, gradient — vary per cell */
}
```

---

## Dark Minimalism

### When to use
Developer tools, crypto/web3, AI products, professional tools.

### Palette
```css
:root {
  --bg: #09090B;        /* near-black */
  --surface: #18181B;   /* card surface */
  --border: #27272A;    /* subtle border */
  --text-primary: #FAFAFA;
  --text-secondary: #A1A1AA;
  --text-muted: #52525B;
  --accent: #22C55E;    /* single neon accent — green/blue/purple */
}
```

### Card
```css
.dark-card {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 12px;
  padding: 24px;
}
.dark-card:hover {
  border-color: #3F3F46;
}
```

### Typography for Dark Minimalism
- Display: Geist (if available) / Outfit 600 / JetBrains Mono 700
- Body: Outfit 400 / Work Sans 400
- Use the single accent colour for key numbers, CTAs, highlights only
- Everything else: near-white text on near-black — no decoration if needed
