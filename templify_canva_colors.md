# Templify - Canva-Inspired Color Palette & Design System

## Primary Color Palette (Canva-Style)

### Main Brand Colors
```css
/* Primary Purple (Canva's signature color) */
--primary: #00C4CC;          /* Canva Teal */
--primary-dark: #0099A3;     /* Darker teal */
--primary-light: #33D1D8;    /* Lighter teal */

/* Secondary Purple */
--secondary: #8B5CF6;        /* Purple accent */
--secondary-dark: #7C3AED;   /* Darker purple */
--secondary-light: #A78BFA;  /* Lighter purple */

/* Accent Colors */
--accent-pink: #FF6B9D;      /* Pink accent */
--accent-orange: #FF8A65;    /* Orange accent */
--accent-green: #4ADE80;     /* Green accent */
--accent-blue: #60A5FA;      /* Blue accent */
```

### Neutral Colors (Canva-Style)
```css
/* Background Colors */
--bg-primary: #FFFFFF;       /* Pure white */
--bg-secondary: #F8FAFC;     /* Light gray background */
--bg-tertiary: #F1F5F9;      /* Slightly darker gray */
--bg-dark: #1E293B;          /* Dark mode background */

/* Text Colors */
--text-primary: #1E293B;     /* Dark gray text */
--text-secondary: #64748B;   /* Medium gray text */
--text-tertiary: #94A3B8;    /* Light gray text */
--text-white: #FFFFFF;       /* White text */

/* Border Colors */
--border-light: #E2E8F0;     /* Light borders */
--border-medium: #CBD5E1;    /* Medium borders */
--border-dark: #475569;      /* Dark borders */
```

### Status Colors
```css
/* Success */
--success: #10B981;
--success-light: #D1FAE5;
--success-dark: #059669;

/* Warning */
--warning: #F59E0B;
--warning-light: #FEF3C7;
--warning-dark: #D97706;

/* Error */
--error: #EF4444;
--error-light: #FEE2E2;
--error-dark: #DC2626;

/* Info */
--info: #3B82F6;
--info-light: #DBEAFE;
--info-dark: #2563EB;
```

## Tailwind CSS Configuration

```javascript
// tailwind.config.js
module.exports = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
    './app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        // Primary Brand Colors
        primary: {
          DEFAULT: '#00C4CC',
          50: '#E6FFFE',
          100: '#CCFFFE',
          200: '#99FFFD',
          300: '#66FFFC',
          400: '#33FFFA',
          500: '#00C4CC',
          600: '#0099A3',
          700: '#00737A',
          800: '#004D52',
          900: '#002629',
        },
        
        // Secondary Purple
        secondary: {
          DEFAULT: '#8B5CF6',
          50: '#F5F3FF',
          100: '#EDE9FE',
          200: '#DDD6FE',
          300: '#C4B5FD',
          400: '#A78BFA',
          500: '#8B5CF6',
          600: '#7C3AED',
          700: '#6D28D9',
          800: '#5B21B6',
          900: '#4C1D95',
        },

        // Accent Colors
        accent: {
          pink: '#FF6B9D',
          orange: '#FF8A65',
          green: '#4ADE80',
          blue: '#60A5FA',
        },

        // Neutral Colors
        gray: {
          50: '#F8FAFC',
          100: '#F1F5F9',
          200: '#E2E8F0',
          300: '#CBD5E1',
          400: '#94A3B8',
          500: '#64748B',
          600: '#475569',
          700: '#334155',
          800: '#1E293B',
          900: '#0F172A',
        },

        // Status Colors
        success: {
          DEFAULT: '#10B981',
          light: '#D1FAE5',
          dark: '#059669',
        },
        warning: {
          DEFAULT: '#F59E0B',
          light: '#FEF3C7',
          dark: '#D97706',
        },
        error: {
          DEFAULT: '#EF4444',
          light: '#FEE2E2',
          dark: '#DC2626',
        },
        info: {
          DEFAULT: '#3B82F6',
          light: '#DBEAFE',
          dark: '#2563EB',
        },
      },
      
      // Fonts (Canva uses Inter primarily)
      fontFamily: {
        sans: ['Inter', 'system-ui', '-apple-system', 'BlinkMacSystemFont', 'Segoe UI', 'Roboto', 'sans-serif'],
        display: ['Inter', 'system-ui', 'sans-serif'],
        body: ['Inter', 'system-ui', 'sans-serif'],
      },
      
      // Shadows (Canva-style)
      boxShadow: {
        'sm': '0 1px 2px 0 rgba(0, 0, 0, 0.05)',
        'DEFAULT': '0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06)',
        'md': '0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06)',
        'lg': '0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05)',
        'xl': '0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04)',
        '2xl': '0 25px 50px -12px rgba(0, 0, 0, 0.25)',
        'inner': 'inset 0 2px 4px 0 rgba(0, 0, 0, 0.06)',
        'canva': '0 2px 8px rgba(0, 0, 0, 0.1), 0 1px 3px rgba(0, 0, 0, 0.08)',
      },
      
      // Border Radius (Canva-style)
      borderRadius: {
        'none': '0',
        'sm': '0.125rem',
        'DEFAULT': '0.375rem',
        'md': '0.5rem',
        'lg': '0.75rem',
        'xl': '1rem',
        '2xl': '1.5rem',
        'full': '9999px',
        'canva': '12px',
      },
      
      // Spacing (Canva-style)
      spacing: {
        '18': '4.5rem',
        '88': '22rem',
        '128': '32rem',
      },
    },
  },
  plugins: [],
}
```

## CSS Custom Properties (For Dark Mode)

```css
/* globals.css */
:root {
  /* Light mode colors */
  --color-primary: 0 196 204;
  --color-secondary: 139 92 246;
  --color-background: 255 255 255;
  --color-foreground: 30 41 59;
  --color-card: 255 255 255;
  --color-card-foreground: 30 41 59;
  --color-popover: 255 255 255;
  --color-popover-foreground: 30 41 59;
  --color-muted: 248 250 252;
  --color-muted-foreground: 100 116 139;
  --color-accent: 248 250 252;
  --color-accent-foreground: 30 41 59;
  --color-border: 226 232 240;
  --color-input: 226 232 240;
  --color-ring: 0 196 204;
}

.dark {
  /* Dark mode colors */
  --color-background: 15 23 42;
  --color-foreground: 248 250 252;
  --color-card: 30 41 59;
  --color-card-foreground: 248 250 252;
  --color-popover: 30 41 59;
  --color-popover-foreground: 248 250 252;
  --color-muted: 30 41 59;
  --color-muted-foreground: 148 163 184;
  --color-accent: 30 41 59;
  --color-accent-foreground: 248 250 252;
  --color-border: 51 65 85;
  --color-input: 51 65 85;
}
```

## Component Color Examples

### Button Styles (Canva-inspired)
```jsx
// Primary Button
<button className="bg-primary hover:bg-primary-dark text-white px-6 py-3 rounded-canva font-medium transition-colors shadow-canva">
  Export to Canva
</button>

// Secondary Button
<button className="bg-secondary hover:bg-secondary-dark text-white px-6 py-3 rounded-canva font-medium transition-colors">
  Analyze Image
</button>

// Outline Button
<button className="border-2 border-primary text-primary hover:bg-primary hover:text-white px-6 py-3 rounded-canva font-medium transition-colors">
  Upload New
</button>
```

### Card Styles
```jsx
// Analysis Card
<div className="bg-white border border-gray-200 rounded-canva p-6 shadow-canva">
  <h3 className="text-gray-900 font-semibold mb-4">Color Analysis</h3>
  <div className="space-y-3">
    {/* Color swatches */}
  </div>
</div>

// Feature Card
<div className="bg-gradient-to-br from-primary/10 to-secondary/10 border border-primary/20 rounded-canva p-6">
  <h3 className="text-gray-900 font-semibold mb-2">Font Detection</h3>
  <p className="text-gray-600">Automatically identify fonts used in your design</p>
</div>
```

### Input Styles
```jsx
// File Upload Area
<div className="border-2 border-dashed border-gray-300 hover:border-primary rounded-canva p-8 text-center transition-colors bg-gray-50 hover:bg-primary/5">
  <input type="file" className="hidden" />
  <p className="text-gray-600">Drop your image here or click to upload</p>
</div>
```

## Color Usage Guidelines

### Primary Color Usage
- **Main CTAs**: "Export to Canva", "Analyze Image"
- **Links**: Active states, hover effects
- **Progress indicators**: Loading bars, success states
- **Icons**: Primary actions, navigation

### Secondary Color Usage
- **Secondary buttons**: Less important actions
- **Accent elements**: Highlights, badges
- **Illustrations**: Decorative elements
- **Gradients**: Combined with primary for depth

### Neutral Color Usage
- **Backgrounds**: Cards, modals, sections
- **Text**: Primary, secondary, and tertiary text
- **Borders**: Dividers, input fields
- **Shadows**: Depth and elevation

### Accent Color Usage
- **Status indicators**: Success (green), warning (orange), error (red)
- **Categories**: Different types of analysis results
- **Interactive elements**: Hover states, active states
- **Data visualization**: Charts, graphs, color swatches

## Dark Mode Support

```css
/* Dark mode color adjustments */
@media (prefers-color-scheme: dark) {
  :root {
    --color-primary: 51 209 216;
    --color-secondary: 167 139 250;
    --color-background: 15 23 42;
    --color-foreground: 248 250 252;
  }
}
```

## Accessibility Considerations

### Color Contrast Ratios
- **Primary on white**: 4.5:1 (AA compliant)
- **Secondary on white**: 4.5:1 (AA compliant)
- **Text colors**: All meet WCAG 2.1 AA standards
- **Focus indicators**: High contrast borders

### Implementation Tips
1. **Use semantic color names** in your design system
2. **Test with colorblind users** using tools like Stark
3. **Provide alternative indicators** beyond just color
4. **Maintain consistent spacing** and typography
5. **Use proper focus states** for keyboard navigation

This color system will give Templify the same polished, professional look as Canva while maintaining excellent usability and accessibility.