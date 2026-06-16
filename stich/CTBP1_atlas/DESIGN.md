---
name: CTBP1 Atlas
colors:
  surface: '#f8f9ff'
  surface-dim: '#cbdbf5'
  surface-bright: '#f8f9ff'
  surface-container-lowest: '#ffffff'
  surface-container-low: '#eff4ff'
  surface-container: '#e5eeff'
  surface-container-high: '#dce9ff'
  surface-container-highest: '#d3e4fe'
  on-surface: '#0b1c30'
  on-surface-variant: '#44474f'
  inverse-surface: '#213145'
  inverse-on-surface: '#eaf1ff'
  outline: '#75777f'
  outline-variant: '#c5c6d0'
  surface-tint: '#495e8a'
  primary: '#00020a'
  on-primary: '#ffffff'
  primary-container: '#001b44'
  on-primary-container: '#7084b3'
  inverse-primary: '#b1c6f9'
  secondary: '#006875'
  on-secondary: '#ffffff'
  secondary-container: '#00e3fd'
  on-secondary-container: '#00616d'
  tertiary: '#010202'
  on-tertiary: '#ffffff'
  tertiary-container: '#191d1f'
  on-tertiary-container: '#818587'
  error: '#ba1a1a'
  on-error: '#ffffff'
  error-container: '#ffdad6'
  on-error-container: '#93000a'
  primary-fixed: '#d8e2ff'
  primary-fixed-dim: '#b1c6f9'
  on-primary-fixed: '#001a42'
  on-primary-fixed-variant: '#314671'
  secondary-fixed: '#9cf0ff'
  secondary-fixed-dim: '#00daf3'
  on-secondary-fixed: '#001f24'
  on-secondary-fixed-variant: '#004f58'
  tertiary-fixed: '#e0e3e5'
  tertiary-fixed-dim: '#c4c7c9'
  on-tertiary-fixed: '#181c1e'
  on-tertiary-fixed-variant: '#434749'
  background: '#f8f9ff'
  on-background: '#0b1c30'
  surface-variant: '#d3e4fe'
typography:
  display-lg:
    fontFamily: Hanken Grotesk
    fontSize: 32px
    fontWeight: '700'
    lineHeight: 40px
    letterSpacing: -0.02em
  headline-md:
    fontFamily: Hanken Grotesk
    fontSize: 20px
    fontWeight: '600'
    lineHeight: 28px
  headline-sm:
    fontFamily: Hanken Grotesk
    fontSize: 16px
    fontWeight: '600'
    lineHeight: 24px
  body-md:
    fontFamily: Inter
    fontSize: 14px
    fontWeight: '400'
    lineHeight: 22px
  body-sm:
    fontFamily: Inter
    fontSize: 12px
    fontWeight: '400'
    lineHeight: 18px
  data-mono:
    fontFamily: JetBrains Mono
    fontSize: 12px
    fontWeight: '500'
    lineHeight: 16px
  label-caps:
    fontFamily: Hanken Grotesk
    fontSize: 11px
    fontWeight: '700'
    lineHeight: 16px
    letterSpacing: 0.05em
rounded:
  sm: 0.125rem
  DEFAULT: 0.25rem
  md: 0.375rem
  lg: 0.5rem
  xl: 0.75rem
  full: 9999px
spacing:
  unit: 4px
  container-padding: 24px
  stack-gap: 12px
  grid-gutter: 16px
  data-row-height: 32px
---

## Brand & Style

The design system for this gene atlas tool centers on a **Corporate / Modern** aesthetic, specifically tailored for academic and clinical research environments. It prioritizes information density and clarity, evoking a sense of precision, reliability, and scientific rigor. 

The visual language is characterized by a "Light Mode" first approach, utilizing a stark white canvas and a high-contrast navy-on-white typographic hierarchy. This creates a crisp, paper-like readability essential for data-heavy views. Interactivity is signaled through a vibrant cyan accent derived from the HADDS Foundation identity, while subtle gray borders replace heavy shadows to maintain a flat, professional, and structured layout. The overall emotional response is one of intellectual authority and functional efficiency.

## Colors

The color palette is anchored by **Navy (#001B44)** for primary branding and headers, and **Cyan (#00E5FF)** for primary actions and interactive highlights. 

To support the complex categorization of genomic data, a specific suite of functional colors is defined. These are adapted for high visibility against a light background, using slightly more saturated tones than the dark-mode reference to ensure accessibility. 
- **Surface:** Pure white (#FFFFFF) is used for the primary background to maximize contrast.
- **Borders:** A refined gray (#E2E8F0) is used for structural division, ensuring the UI feels organized without appearing cluttered.
- **Functional States:** Success, Warning, and Error states utilize standard semantic palettes but are tuned to the specific weights of the Gene Category colors to maintain a cohesive visual rhythm.

## Typography

This design system employs a multi-font approach to differentiate between branding, reading, and technical data. 

- **Hanken Grotesk** is used for headlines and navigation to provide a sharp, contemporary professional feel.
- **Inter** is utilized for all body copy and descriptions, chosen for its exceptional legibility in dense text environments.
- **JetBrains Mono** is reserved for identifiers (e.g., Ensembl IDs, UniProt codes) and raw data values, signaling to the user that these are technical strings.

The hierarchy is intentionally "tight," with smaller base font sizes (14px/12px) to allow for the display of maximum information without horizontal scrolling. Mobile views compress headings by 20% to accommodate portrait aspect ratios.

## Layout & Spacing

The layout follows a **Fixed Grid** system for dashboard views and a **Fluid Content** model for data visualizations. 

- **Desktop:** 12-column grid with 16px gutters. Sidebars are fixed at 280px to ensure filter controls remain accessible during horizontal exploration.
- **Density:** We utilize a "Compact" spacing rhythm. Vertical margins are kept to a minimum (typically 12px or 16px) to keep related data clusters within the same viewport.
- **Data Rows:** Standardized row heights of 32px are used for lists and tables to ensure high-density information remains scannable and touch-target compliant.

## Elevation & Depth

This design system minimizes the use of shadows to maintain a clean, academic appearance. Depth is conveyed through:

1.  **Tonal Layers:** The primary background is White (#FFFFFF). Secondary panels and headers use a very light tint of Navy or Gray (#F8FAFC) to create a subtle "recessed" effect.
2.  **Low-Contrast Outlines:** Instead of shadows, cards and interactive zones are defined by 1px borders in Light Gray (#E2E8F0). 
3.  **Active Elevation:** Only the most critical temporary elements (tooltips, dropdowns) utilize a soft, 4px blur ambient shadow with a 5% Navy tint to differentiate them from the flat underlying data layer.

## Shapes

The shape language is **Soft (0.25rem)**. 

This minimal rounding provides a modern touch without sacrificing the "serious" nature of the tool. It allows for efficient tile packing in grid views. 
- **Buttons and Inputs:** 4px (0.25rem) radius.
- **Large Container Cards:** 8px (0.5rem) radius.
- **Data Tags/Chips:** Fully pill-shaped (1rem+) to distinguish them from interactive buttons.

## Components

- **Buttons:** Primary buttons use a solid Navy background with white text. Secondary buttons use a Cyan outline with Navy text. Hover states for both include a 10% Cyan overlay.
- **Gene Category Chips:** These feature a solid color indicator (the functional palette) paired with a light tint of the same color as the background and dark text for maximum legibility.
- **Data Tables:** Headers are Navy with White text, using `label-caps` typography. Rows use alternating subtle gray backgrounds (zebra striping) for readability in wide datasets.
- **Search/Input Fields:** Use a white background with a 1px gray border. On focus, the border transitions to Cyan with a 2px Cyan outer glow (soft).
- **Discovery Cards:** These components (at the bottom of the reference) should feature a top-border accent matching the functional color of the gene category they represent, reinforcing the classification system.
- **Sidebars:** Grouped filters use "Accordion" style headers with Navy `headline-sm` type and subtle dividers between sections.