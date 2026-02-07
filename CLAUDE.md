# CLAUDE.md - AI Assistant Guidelines for Calcolo Diluizioni

## Project Overview

**Calcolo Diluizioni** (Dilution Calculator) is a single-page web application for calculating product dilution ratios, primarily targeting car care and detailing products. The app is written entirely in Italian.

## Quick Start

```bash
# No build process required - open directly in browser
open diluizioni.html
# Or serve via any HTTP server
python -m http.server 8000
```

## Project Structure

```
calcolo-diluizioni/
├── CLAUDE.md           # This file - AI assistant guidelines
├── README.md           # Brief project description (Italian)
└── diluizioni.html     # Complete application (single file, ~784 lines)
```

### File Organization (diluizioni.html)

| Section | Lines | Description |
|---------|-------|-------------|
| HTML Head & Meta | 1-6 | Document declaration, UTF-8, viewport |
| CSS Styles | 7-320 | Complete inline stylesheet |
| HTML Structure | 321-445 | Tab-based UI with three sections |
| JavaScript | 447-781 | Application logic and data |
| Closing Tags | 782-783 | `</body>` and `</html>` |

## Architecture

### Technology Stack

- **HTML5** - Semantic markup
- **CSS3** - Inline styles with flexbox/grid, responsive design
- **Vanilla JavaScript (ES6+)** - No frameworks or dependencies
- **Zero build process** - Runs directly in browser

### Key Design Decisions

1. **Single-file architecture**: All HTML, CSS, and JS in one file for simplicity
2. **No external dependencies**: Zero HTTP requests beyond initial HTML load
3. **Mobile-first**: Container max-width 480px with responsive adjustments
4. **State-driven UI**: Simple state variables (`selectedVolume`, `selectedRatio`, etc.)

## Core Features

### 1. Calcolatore (Calculator Tab)
- Main dilution calculator using ratio format 1:X
- Preset buttons for common volumes and ratios
- Auto-calculation when presets selected

### 2. Rabbocco (Refill Tab)
- Calculates how to refill bottles with existing diluted product
- Validates feasibility and shows appropriate error messages
- Tracks existing concentrate in bottle

### 3. Riferimento Prodotti (Products Tab)
- Reference guide for 8 supported cleaning products (1 Cleantle + 7 Labocosmetica)
- Each product has multiple usage scenarios with recommended ratios
- Clicking a use auto-sets the ratio and switches to Calculator tab

## Code Conventions

### Naming

- **Functions**: camelCase (`calculateRefill`, `renderVolumePresets`)
- **Variables**: camelCase (`selectedVolume`, `volumePresets`)
- **CSS Classes**: kebab-case (`tab-content`, `btn-primary`, `result-row`)
- **IDs**: kebab-case (`calc-result`, `product-select`)

### JavaScript Patterns

```javascript
// State management - simple variables
let selectedVolume = '';
let selectedRatio = '';

// Render functions - return HTML strings via template literals
function renderVolumePresets() {
  container.innerHTML = volumePresets.map(v =>
    `<button class="preset-btn" onclick="selectVolume(${v})">${formatVolume(v)}</button>`
  ).join('');
}

// Event handlers - inline onclick for presets, addEventListener for inputs
document.getElementById('volume').addEventListener('input', function() { ... });
```

### CSS Color System (Tailwind-inspired)

- Primary Blue: `#2563eb`
- Cyan: `#0891b2`
- Green: `#16a34a`
- Red: `#dc2626`
- Gray scale: `#f9fafb` to `#1f2937`

## Key Calculation Formulas

### Basic Dilution (1:X ratio)
```javascript
// For ratio 1:X, total parts = 1 + X
totalParts = 1 + ratio;
product = volume / totalParts;
water = product * ratio;
```

### Refill Calculation
```javascript
existingConcentrate = remaining / (1 + currentRatio);
totalConcentrateNeeded = bottle / (1 + targetRatio);
concentrateToAdd = totalConcentrateNeeded - existingConcentrate;
volumeToAdd = bottle - remaining;
waterToAdd = volumeToAdd - concentrateToAdd;
```

## Common Development Tasks

### Adding a New Product

Add to the `products` array (lines 453-533):

```javascript
{
  name: "Product Name",
  category: "Category",
  uses: [
    { name: "Use case 1", ratio: 10 },
    { name: "Use case 2", ratio: "5-10", ratioValue: 5 }, // Range with default
  ]
}
```

Notes:
- `ratio` can be a number or string (for ranges like "5-10")
- When using a range string, provide `ratioValue` for the default click value

### Adding a Preset Ratio

Add to `ratioPresets` array (line 450):
```javascript
const ratioPresets = [1, 2, 3, ..., 1200, YOUR_NEW_RATIO];
```

### Adding a Preset Volume

Add to `volumePresets` array (line 449):
```javascript
const volumePresets = [500, 1000, 2000, ..., YOUR_NEW_VOLUME];
```

### Adding a Bottle Preset

Add to `bottlePresets` array (line 451):
```javascript
const bottlePresets = [500, 750, 1000, YOUR_NEW_BOTTLE];
```

### Modifying Calculations

- Basic dilution: `calculate()` function (lines 635-655)
- Refill logic: `calculateRefill()` function (lines 658-718)

### Styling Changes

All CSS is inline in the `<style>` block (lines 7-320). Key classes:
- `.card` - Main container
- `.tab` / `.tab-content` - Tab system
- `.preset-btn` - Preset buttons
- `.result` - Result display boxes
- `.btn-primary` - Main action button

## Data Structures

### Product Schema
```javascript
{
  name: string,           // Product name
  category: string,       // Category (e.g., "APC", "Shampoo")
  uses: [{
    name: string,         // Use case description
    ratio: number|string, // Ratio value or range
    ratioValue?: number   // Optional: default value for ranges
  }]
}
```

### Presets
```javascript
volumePresets = [500, 1000, 2000, 5000, 8000, 10000, 12000];     // ml
ratioPresets = [1,2,3,...,10,15,20,...,100,200,300,400,800,1000,1200]; // 29 values
bottlePresets = [500, 750, 1000];                                  // ml (refill tab)
```

### Current Products (as of last update)

1. Cleantle EcoWash (Multi-uso) - 5 uses
2. Labocosmetica Ductile (APC) - 4 uses
3. Labocosmetica Energo (Decontaminante Calcare) - 2 uses
4. Labocosmetica Omnia (Interni) - 2 uses
5. Labocosmetica Primus (Prelavaggio) - 8 uses
6. Labocosmetica Purifica (Shampoo) - 5 uses
7. Labocosmetica Revitax (Shampoo) - 2 uses
8. Labocosmetica Semper (Shampoo) - 3 uses

### Key Functions Reference

| Function | Line | Purpose |
|----------|------|---------|
| `init()` | 542 | App initialization, renders all presets and sets up tabs |
| `formatVolume(ml)` | 550 | Formats ml to "Xml" or "XL" display |
| `renderVolumePresets()` | 555 | Renders volume preset buttons |
| `renderRatioPresets()` | 562 | Renders ratio preset buttons |
| `renderBottlePresets()` | 569 | Renders bottle preset buttons |
| `renderProductSelect()` | 576 | Populates product dropdown |
| `setupTabs()` | 583 | Wires up tab click handlers |
| `selectVolume(v)` | 595 | Handles volume preset selection |
| `selectRatio(r)` | 602 | Handles ratio preset selection |
| `selectBottle(b)` | 610 | Handles bottle preset selection |
| `calculate()` | 635 | Main dilution calculation |
| `calculateRefill()` | 658 | Refill/top-up calculation |
| `onProductChange(e)` | 721 | Product dropdown change handler |
| `selectProductUse(name, ratioValue)` | 742 | Sets ratio from product use and switches to calculator |

## Testing

No automated tests exist. Manual testing procedure:
1. Open `diluizioni.html` in browser
2. Test each tab's functionality
3. Verify calculations with known values
4. Test edge cases (empty inputs, impossible dilutions)
5. Test on mobile viewport

Example verification:
- 1L (1000ml) with ratio 1:10 should yield:
  - Product: 90.91 ml
  - Water: 909.09 ml

## Language

The application is entirely in **Italian**. Key terms:
- Calcolatore = Calculator
- Diluizione = Dilution
- Rapporto = Ratio
- Rabbocco = Refill
- Prodotto = Product
- Acqua = Water
- Flacone = Bottle
- Quantità = Quantity

## Browser Support

- Modern evergreen browsers (Chrome, Firefox, Safari, Edge)
- Mobile-responsive (viewport meta tag included)
- Requires JavaScript enabled

## Important Notes for AI Assistants

1. **Single file**: All changes go in `diluizioni.html`
2. **No build step**: Changes are immediately testable by refreshing browser
3. **Italian language**: Maintain Italian for all user-facing text
4. **Keep it simple**: No external dependencies, no frameworks
5. **Mobile-first**: Test any UI changes at narrow viewport widths
6. **Preserve structure**: Keep CSS/HTML/JS sections in their current order
7. **Calculation accuracy**: Double-check math formulas - users rely on these for real measurements

## Git Workflow

- Main branch contains production code
- Feature branches for new development
- Commit messages can be in Italian or English
- No CI/CD pipeline - manual deployment
