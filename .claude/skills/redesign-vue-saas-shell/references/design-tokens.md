# Design Tokens

Complete token reference for the SaaS shell. Place these as CSS custom properties on `:root` (or in your CSS-in-JS / Tailwind config). Token names are stable; values are starting points — tune to your brand.

## How to use this file

- **Starting fresh?** Copy the "Minimum viable subset" at the bottom. ~12 tokens get you a working shell. Expand as needed.
- **Migrating an existing app?** Use the full tables below as a target. Map each existing hardcoded value to the closest token name. Don't invent new tokens unless a real semantic gap exists.
- **Component library users (Vuetify, Tailwind, etc.):** mirror these names into your library's theme config. The names are what matter; the storage mechanism is interchangeable.

---

## Color

### Neutral surface

| Token                  | Light   | Dark    | Use                                                |
| ---------------------- | ------- | ------- | -------------------------------------------------- |
| `--color-bg-app`       | #f8fafc | #0b1220 | App background outside cards                       |
| `--color-bg-surface`   | #ffffff | #111827 | Cards, modals, popovers                            |
| `--color-bg-sunken`    | #f1f5f9 | #0a0f1a | Inset surfaces (table headers, code)               |
| `--color-bg-sidebar`   | #0f172a | #0a0f1a | Sidebar background (intentionally dark even in LT) |
| `--color-fg-primary`   | #0f172a | #e2e8f0 | Primary text                                       |
| `--color-fg-secondary` | #475569 | #94a3b8 | Muted text, labels                                 |
| `--color-fg-tertiary`  | #94a3b8 | #64748b | Disabled, placeholders                             |
| `--color-fg-on-dark`   | #e2e8f0 | #e2e8f0 | Text on sidebar / dark surfaces                    |
| `--color-border`       | #e2e8f0 | #1e293b | Hairline borders                                   |
| `--color-border-soft`  | #f1f5f9 | #1e293b | Inner dividers                                     |

### Accent

| Token                  | Value   | Use                                  |
| ---------------------- | ------- | ------------------------------------ |
| `--color-accent`       | #2563eb | Primary actions, active nav, links   |
| `--color-accent-hover` | #1d4ed8 | Hover state                          |
| `--color-accent-soft`  | #dbeafe | Subtle accent backgrounds (LT)       |
| `--color-focus-ring`   | #3b82f6 | Focus outline; should be high enough contrast on every surface |

### Semantic / status

| Token              | Value   | Use                          |
| ------------------ | ------- | ---------------------------- |
| `--color-success`  | #16a34a | Delivered, healthy, positive |
| `--color-warning`  | #ca8a04 | Pending, low stock           |
| `--color-danger`   | #dc2626 | Backordered, errors          |
| `--color-info`     | #0284c7 | Informational badges         |
| `--color-success-soft` | #dcfce7 | Soft fill for success badges |
| `--color-warning-soft` | #fef9c3 | Soft fill for warning badges |
| `--color-danger-soft`  | #fee2e2 | Soft fill for danger badges  |
| `--color-info-soft`    | #e0f2fe | Soft fill for info badges    |

---

## Spacing — 4px base scale

| Token       | Value    | Common use                                    |
| ----------- | -------- | --------------------------------------------- |
| `--space-0` | 0        | Reset                                         |
| `--space-1` | 0.25rem  | Icon-to-label gap (tight)                     |
| `--space-2` | 0.5rem   | Inline gaps, badge padding                    |
| `--space-3` | 0.75rem  | Sidebar nav-link padding-x, button padding-x  |
| `--space-4` | 1rem     | Card inner padding (compact)                  |
| `--space-5` | 1.5rem   | Card inner padding (default), section gaps    |
| `--space-6` | 2rem     | Page padding, large section gaps              |
| `--space-7` | 3rem     | Hero spacing                                  |
| `--space-8` | 4rem     | (Use sparingly)                               |

**Rule of thumb:** if your design needs `1.25rem`, round to `--space-5` (1.5rem). Resist creating `--space-4-5`.

---

## Typography

### Family

| Token            | Value                                                                         |
| ---------------- | ----------------------------------------------------------------------------- |
| `--font-sans`    | `Inter, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif`     |
| `--font-mono`    | `ui-monospace, SFMono-Regular, "SF Mono", Menlo, Consolas, monospace`         |

### Size

| Token         | Value     | Use                              |
| ------------- | --------- | -------------------------------- |
| `--text-xs`   | 0.75rem   | Eyebrows, captions               |
| `--text-sm`   | 0.875rem  | Sidebar nav, table cells, labels |
| `--text-base`| 0.95rem   | Body                             |
| `--text-md`   | 1rem      | Emphasized body                  |
| `--text-lg`   | 1.125rem  | Card titles                      |
| `--text-xl`   | 1.375rem  | Page titles                      |
| `--text-2xl`  | 1.875rem  | Hero / dashboard KPI numbers     |

### Weight

| Token              | Value | Use                      |
| ------------------ | ----- | ------------------------ |
| `--font-regular`   | 400   | Body                     |
| `--font-medium`    | 500   | Nav links, table headers |
| `--font-semibold`  | 600   | Section titles, buttons  |
| `--font-bold`      | 700   | Page titles, KPI numbers |

### Line height

| Token           | Value | Use      |
| --------------- | ----- | -------- |
| `--leading-tight` | 1.2  | Headings |
| `--leading-snug`  | 1.4  | Labels   |
| `--leading-normal`| 1.6  | Body     |

---

## Radius / shadow / border

| Token              | Value                                  | Use                                |
| ------------------ | -------------------------------------- | ---------------------------------- |
| `--radius-sm`      | 6px                                    | Buttons, nav links, badges         |
| `--radius-md`      | 10px                                   | Cards, inputs                      |
| `--radius-lg`      | 16px                                   | Modals                             |
| `--radius-pill`    | 999px                                  | Pills, avatars                     |
| `--shadow-pop`     | `0 4px 16px rgba(15, 23, 42, 0.08)`    | Popovers, dropdowns                |
| `--shadow-elevate` | `0 8px 32px rgba(15, 23, 42, 0.12)`    | Modals                             |
| `--shadow-flat`    | `0 1px 0 rgba(15, 23, 42, 0.04)`       | Subtle card lift (or omit entirely)|

Prefer borders + spacing over shadows. Use shadows only for elevated layers (popovers, modals).

---

## Sidebar / layout geometry

| Token                    | Value | Use                                  |
| ------------------------ | ----- | ------------------------------------ |
| `--sidebar-w-expanded`   | 260px | Expanded sidebar width               |
| `--sidebar-w-collapsed`  | 64px  | Collapsed sidebar width              |
| `--sidebar-w`            | `var(--sidebar-w-expanded)` | Active width — swapped on collapse |
| `--page-header-h`        | 56px  | Optional in-content sticky header    |
| `--content-max-w`        | 1280px| Max content width inside main area   |
| `--content-padding-x`    | `var(--space-6)` | Horizontal padding of main      |

---

## Motion

| Token              | Value                              | Use                          |
| ------------------ | ---------------------------------- | ---------------------------- |
| `--motion-fast`    | 120ms                              | Hover states, focus rings    |
| `--motion-base`    | 200ms                              | Sidebar collapse, route fade |
| `--motion-slow`    | 320ms                              | Modal enter/exit             |
| `--ease-out`       | `cubic-bezier(0.2, 0.8, 0.2, 1)`   | Default                      |
| `--ease-in-out`    | `cubic-bezier(0.4, 0, 0.2, 1)`     | Symmetric transitions        |

Always pair with `prefers-reduced-motion` media query to zero out durations.

---

## Z-index scale

| Token              | Value | Use                               |
| ------------------ | ----- | --------------------------------- |
| `--z-base`         | 0     | Default                           |
| `--z-sidebar`      | 10    | Sidebar over content              |
| `--z-page-header`  | 20    | Sticky page header / FilterBar    |
| `--z-mobile-scrim` | 40    | Mobile drawer backdrop            |
| `--z-drawer`       | 50    | Mobile drawer                     |
| `--z-popover`      | 60    | Profile/locale popovers           |
| `--z-modal`        | 80    | Detail modals                     |
| `--z-toast`        | 90    | Toasts / snackbars                |

---

## Minimum viable subset

If you want to ship the redesign with as few tokens as possible, start with these 12 and grow incrementally:

```css
:root {
  /* Color */
  --color-bg-app:       #f8fafc;
  --color-bg-surface:   #ffffff;
  --color-bg-sidebar:   #0f172a;
  --color-fg-primary:   #0f172a;
  --color-fg-secondary: #64748b;
  --color-fg-on-dark:   #e2e8f0;
  --color-border:       #e2e8f0;
  --color-accent:       #2563eb;

  /* Spacing */
  --space-3: 0.75rem;
  --space-5: 1.5rem;

  /* Sidebar */
  --sidebar-w-expanded:  260px;
  --sidebar-w-collapsed: 64px;
}
```

Add tokens when a real, recurring need shows up — not preemptively.
