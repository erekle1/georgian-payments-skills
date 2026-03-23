# TBC Merchant Design Guidelines

Branding and UI requirements for merchants integrating TBC payment components.

Source: `https://developers.tbcbank.ge/docs/design`

---

## Color Palette

| Token | Hex | Usage |
|-------|-----|-------|
| TBC Blue (primary) | `#00AEEF` | CTAs, links, focused inputs, active indicators |
| Dark Blue | `#0C2B43` | Text, headings, dark elements |
| White | `#FFFFFF` | Backgrounds, text on dark |
| Gray 1 | `#737373` | Secondary text, descriptions |
| Background Gray | `#FAFBFC` | Card backgrounds, sections |
| Error Red | `#eb4949` | Validation errors, error borders |
| Dark Gray | `#555F62` | Monochrome icons/elements |
| Border Gray | `#DADADA` | Default input borders, dividers |

---

## Typography

**Font family:** `TBC Sailec Regular`

Font CDN: `https://openapiportalobjects.tbcbank.ge/fonts/`

| Level | Size | Weight | Color |
|-------|------|--------|-------|
| Main Title | 40px | Bold, uppercase | `#0E2536` |
| Blue Title | 24px | Normal, uppercase | `#00AEEF` |
| H2 | 15px | Bold | `#0E2536` |
| H3 | — | 700 | `#555` |
| Card Title | 18px | — | `#0C2B43` |
| Body/Description | 14px | Normal | `#737373` |
| Date/Accent | 14px | Bold | `#00AEEF` |

---

## Payment Button Styles

TBC logo SVG: 24×24px viewBox. Four button variants:

### 1. TBC Blue (Primary CTA)
```css
.tbcButtonBlue {
  background: #00AEEF;
  color: #FFFFFF;
}
```
Use for primary "Pay with TBC" actions.

### 2. Light (Secondary)
```css
.tbcButtonLight {
  background: #FFFFFF;
  border-color: #00AEEF;
  color: #00AEEF;
}
```
White background with blue border and text.

### 3. Light Monochrome (Tertiary)
```css
.tbcButtonLightMonochrome {
  color: #555F62;
}
```
Minimal contrast, for secondary actions on light backgrounds.

### 4. Dark Monochrome (Inverse)
```css
.tbcButtonDarkMonochrome {
  background: dark;
  color: #FFFFFF;
}
```
For use on dark backgrounds.

### Button Layout Options
- Full width: text + icon
- Text only: `.upperCase` or lowercase
- Icon only: `.tbcButtonIconOnly`

### CSS Classes
```
.tbcButton              /* Base */
.tbcButtonBlue          /* Primary */
.tbcButtonLight         /* Secondary */
.tbcButtonLightMonochrome  /* Tertiary */
.tbcButtonDarkMonochrome   /* Inverse */
.tbcButtonIconOnly      /* Icon only */
.upperCase              /* Uppercase text */
```

---

## Text Input Component

| Property | Value |
|----------|-------|
| Width | 300px |
| Height | 50px (default), 30px (focused) |
| Border | 2px solid |
| Default border | `#DADADA` |
| Focused border | `#00AEEF` |
| Error border | `#eb4949` |
| Disabled bg | `#FAFAFA` |

Features: animated labels, error states with red icon + message, validation indicators.

---

## Card Component

```css
.card {
  background: #FAFBFC;
  border: 1px solid #DCDCDC;
  border-radius: 4px;
}
.card:hover {
  background: #FFFFFF;
  box-shadow: 0px 16px 32px 8px rgba(0, 0, 0, 0.05);
}
```

- Image height: 188–200px
- Title: 18px, `#0C2B43`, 72px height
- Description: 14px, `#737373`, min 96px
- Date: 14px, bold, `#00AEEF`

---

## Layout

| Token | Value |
|-------|-------|
| Container max-width | 1140px |
| Container-lg | 1440px |
| Standard shadow | `0px 16px 32px 8px rgba(0, 0, 0, 0.05)` |
| Transition | 0.5s default, 0.3s mobile menu |

### Responsive Breakpoints
- `990px` — Hide desktop nav
- `768px` — Mobile layout, hide header
- `770px` — Adjust search, footer

---

## Logo

- Header logo: 45×40px
- Source: `https://files.readme.io/106fbe7-small-bluelogo_v1.png`
- SVG button icon: 24×24px, fill colors vary by button variant

---

## Links

- Color: `#00AEEF`
- Hover: darker variant
- Text-decoration: none
