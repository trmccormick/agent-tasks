# EVE Dashboard CSS Reference Guide for Galaxy Game UI

**Purpose**: Quick reference for CSS patterns that Qwen can study from EVE Dashboard for Galaxy Game UI prototype development.

**Important**: We're borrowing ONLY visual aesthetics and styling patterns. DO NOT copy EVE-specific logic or gameplay concepts.

---

## File Locations

- **Main stylesheet**: `/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css`
- **Base template**: `/Users/tam0013/Documents/git/eve-dashboard/app/templates/base.html`
- **Key templates to study**: `dashboard.html`, `fleet.html`, `market.html` (for layout ideas only)

---

## Color Palette (Extract from style.css lines 1-20)

```css
:root {
  --bg: #0e1114;              /* Main background */
  --panel: #161b21;           /* Panel/card background */
  --panel2: #1d242c;          /* Alt panel background */
  --line: #2a333d;            /* Borders & dividers */
  --text: #d8dee6;            /* Main text color */
  --muted: #8a97a6;           /* Secondary text */
  --accent: #4aa8ff;          /* Bright cyan accent */
  --green: #3fb950;           /* Success/positive */
  --red: #f85149;             /* Danger/negative */
  --gold: #e3b341;            /* Warning/accent 2 */
}
```

**Mapping to Galaxy Game UI Tailwind**:
- `--bg` → `eve-dark` (#0e1114)
- `--panel` → `eve-panel` (#161b21)
- `--panel2` → `eve-panel2` (#1d242c)
- `--line` → `eve-line` (#2a333d)
- `--text` → `eve-text` (#d8dee6)
- `--muted` → `eve-muted` (#8a97a6)
- `--accent` → `eve-accent` (#4aa8ff)
- `neon-cyan` → #00d9ff (brighter, for Galaxy Game highlights)
- `neon-purple` → #b366ff (for admin sections)
- `neon-gold` → #ffd700 (for economic data)

---

## Key CSS Patterns to Study

### 1. **Panel Component** (cards, sections)

**EVE Dashboard pattern** (lines 130-135):
```css
.panel {
  background: var(--panel);
  border: 1px solid var(--line);
  border-radius: 10px;
  padding: 24px;
}
```

**Galaxy Game UI usage**:
```css
/* Create reusable glass card component */
.glass-card {
  background: rgba(22, 27, 33, 0.8);      /* eve-panel with slight transparency */
  border: 1px solid rgba(74, 168, 255, 0.2);  /* eve-accent with transparency */
  border-radius: 0.75rem;                 /* Tailwind: rounded-lg */
  padding: 1.5rem;
  backdrop-filter: blur(10px);            /* Glassmorphic effect */
}
```

### 2. **Badge/Status Styling**

**EVE Dashboard inspiration** (lines 236-242):
```css
.btn {
  display: inline-block;
  padding: 7px 14px;
  border-radius: 6px;
  border: 1px solid var(--line);
  background: var(--panel2);
  color: var(--text);
}

.btn:hover {
  border-color: var(--accent);
}
```

**Galaxy Game UI usage for status badges**:
```css
.status-badge {
  display: inline-flex;
  padding: 0.375rem 0.75rem;
  border: 1px solid;
  border-radius: 0.25rem;
  font-size: 0.875rem;
  font-weight: 500;
}

/* Specific status colors */
.status-harvesting {
  background: rgba(63, 185, 80, 0.2);      /* eve-green with transparency */
  border-color: rgba(63, 185, 80, 0.5);
  color: #3fb950;
}

.status-in-transit {
  background: rgba(74, 168, 255, 0.2);     /* eve-accent with transparency */
  border-color: rgba(74, 168, 255, 0.5);
  color: #4aa8ff;
}
```

### 3. **Table Styling**

**EVE Dashboard pattern** (lines 254-256):
```css
table.grid {
  border-collapse: collapse;
  width: 100%;
  font-size: 13px;
}
```

**Galaxy Game UI usage**:
```css
.data-table {
  width: 100%;
  border-collapse: collapse;
}

.data-table thead {
  border-bottom: 2px solid rgba(74, 168, 255, 0.3);
}

.data-table th {
  color: #4aa8ff;                         /* eve-accent */
  text-align: left;
  padding: 0.75rem;
  font-weight: 600;
  font-size: 0.875rem;
}

.data-table td {
  padding: 0.75rem;
  border-bottom: 1px solid rgba(74, 168, 255, 0.1);
}

.data-table tbody tr:hover {
  background: rgba(74, 168, 255, 0.05);   /* Subtle hover effect */
}
```

### 4. **Scrollbar Styling**

**EVE Dashboard pattern** (lines 37-50):
```css
* {
  scrollbar-width: thin;
  scrollbar-color: rgba(74, 168, 255, .5) transparent;
}

::-webkit-scrollbar {
  width: 10px;
  height: 10px;
}

::-webkit-scrollbar-thumb {
  background-color: rgba(74, 168, 255, .5);
  background-clip: padding-box;
  border: 2px solid transparent;
  border-radius: 999px;
}
```

**Galaxy Game UI usage** (add to global CSS):
```css
* {
  scrollbar-width: thin;
  scrollbar-color: rgba(74, 168, 255, 0.5) transparent;
}

::-webkit-scrollbar {
  width: 10px;
  height: 10px;
}

::-webkit-scrollbar-track {
  background: transparent;
}

::-webkit-scrollbar-thumb {
  background-color: rgba(74, 168, 255, 0.5);
  background-clip: padding-box;
  border: 2px solid transparent;
  border-radius: 999px;
}

::-webkit-scrollbar-thumb:hover {
  background-color: #4aa8ff;
}
```

### 5. **Progress Bar / Fill Indicator**

**Galaxy Game UI custom** (for inventory progress bars):
```css
.progress-bar {
  background: rgba(74, 168, 255, 0.1);   /* eve-accent with low opacity */
  border: 1px solid rgba(74, 168, 255, 0.2);
  border-radius: 0.25rem;
  height: 1.5rem;
  overflow: hidden;
}

.progress-fill {
  background: linear-gradient(90deg, #4aa8ff, #b366ff);  /* eve-accent to neon-purple */
  height: 100%;
  transition: width 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.75rem;
  font-weight: 600;
  color: #0e1114;  /* eve-dark text for contrast */
}
```

---

## Design Philosophy to Apply

1. **Transparency + Blur** (Glassmorphic effect)
   - Use `rgba()` colors with low opacity (0.1-0.3 for backgrounds, 0.2-0.5 for borders)
   - Apply `backdrop-filter: blur(10px)` to panels and overlays
   - Creates depth and visual separation without harsh lines

2. **Accent Color for Interactivity**
   - `--accent: #4aa8ff` (eve-accent) is the primary interactive color
   - Use it for links, hover states, active states, and highlights
   - DO NOT use it for passive text—use `--text` or `--muted` instead

3. **Consistent Spacing**
   - EVE uses: padding 24px for large panels, 12px for medium, 7px for buttons
   - Galaxy Game UI uses Tailwind defaults, but maintain visual hierarchy

4. **Dark Theme Accessibility**
   - Text: `#d8dee6` (eve-text) on `#161b21` (eve-panel) = good contrast
   - Accent (`#4aa8ff`) on dark backgrounds = excellent contrast
   - Use `--muted` (#8a97a6) only for secondary/tertiary information

5. **Borders + Shadows**
   - Subtle borders (`rgba(..., 0.2)` of accent color)
   - Minimal shadows, or use `box-shadow: 0 4px 12px rgba(74, 168, 255, 0.1)` for subtle glow
   - Avoid harsh/dark shadows—they don't fit the sci-fi aesthetic

---

## DO NOT Copy From EVE Dashboard

❌ EVE-specific layouts (character sheets, market order books, PI production chains)  
❌ EVE terminology (Venture, Orca, ISK-per-second calculations, security status colors)  
❌ EVE game mechanics (missions, reputation, NPC corporations)  
❌ JavaScript logic for EVE API calls or ESI integration  

✅ **DO COPY**: Color scheme, panel styling, badge components, table layouts, scrollbar theming, button styles, glassmorphic effects

---

## Quick Checklist for Tasks 1-4

**Task 1 (Rails 8 Setup)**:
- [ ] Tailwind config uses eve-* color names correctly
- [ ] Base layout template uses eve-dark, eve-panel, eve-accent, eve-text
- [ ] Test: `bg-eve-dark text-eve-text` renders dark theme correctly
- [ ] Scrollbar styling applied to global CSS

**Task 2 (Dashboard)**:
- [ ] All panels use `.glass-card` component (Glassmorphic effect)
- [ ] Status badges use eve-green/eve-accent/eve-gold per status type
- [ ] Progress bars use gradient from eve-accent to neon-purple
- [ ] Table headers use eve-accent color, rows have subtle hover effect

**Task 3 (Wormholes)**:
- [ ] Node map table follows EVE Dashboard table pattern
- [ ] Route cards use glass-card styling with left border accent
- [ ] Stability indicator bars use eve-accent gradient

**Task 4 (Admin Panel)**:
- [ ] Directive progress bars follow same pattern as Dashboard inventory
- [ ] Economic indices stat boxes use glass-card styling centered
- [ ] Event log uses eve-panel2 background with transparent accents
- [ ] Toast notifications use eve-accent background with eve-dark text

---

## External Resources

- **EVE Dashboard GitHub**: https://github.com/ArtificialQualia/EVE-Dashboard (reference only, don't clone)
- **Full stylesheet**: `/Users/tam0013/Documents/git/eve-dashboard/app/static/style.css` (read in full)
- **Tailwind Documentation**: https://tailwindcss.com/docs (for Tailwind utility classes)

