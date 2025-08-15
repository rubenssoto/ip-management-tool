# TICKET #3: Frontend Foundation & Layout

**Priority:** High | **Story Points:** 5 | **Estimated Time:** 6-8 hours

## Overview
Create the foundational HTML structure, CSS framework setup, and basic layout components for the IP Allowlist Manager web application.

## File Structure to Create

### HTML Templates (in Google Apps Script)

#### Required Files:
- `index.html` - Main application layout
- `styles.html` - CSS includes and custom styles
- `app.js.html` - JavaScript application logic
- `components.html` - Reusable UI components

## Design System Specifications

### Color Palette
**Requirement:** Define CSS custom properties (variables) for a consistent color system including:
- Primary colors: 5 shades from light blue (#eff6ff) to dark blue (#1d4ed8)
- Gray scale: 9 shades from very light gray (#f9fafb) to very dark gray (#111827)
- Status colors: Success green (#10b981), warning yellow (#f59e0b), error red (#ef4444)
- Use standard CSS :root selector to define these as global variables

### Typography Scale
- **Headings:** font-weight: 600, line-height: 1.25
- **Body:** font-weight: 400, line-height: 1.6  
- **Labels:** font-weight: 500, line-height: 1.4
- **Font Stack:** system-ui, -apple-system, "Segoe UI", Roboto, sans-serif

### Spacing System (Tailwind-based)
- **Base unit:** 0.25rem (4px)
- **Scale:** 1, 2, 3, 4, 5, 6, 8, 10, 12, 16, 20, 24, 32, 40, 48, 64

## Tasks Checklist

### ✅ Base HTML Structure (index.html)
- [ ] Create semantic HTML5 structure
- [ ] Set up responsive viewport meta tag
- [ ] Include Tailwind CSS (CDN or compiled)
- [ ] Create main layout grid
- [ ] Add accessibility attributes (lang, roles)

### ✅ Navigation Header
- [ ] Company logo/app title area
- [ ] User info display (email, role indicator)
- [ ] Admin mode toggle (if admin)
- [ ] Responsive mobile menu
- [ ] Logout functionality

### ✅ Main Content Layout
- [ ] Flexible content area with max-width container
- [ ] Sidebar for filters/actions (collapsible on mobile)
- [ ] Main data display area
- [ ] Proper spacing and visual hierarchy

### ✅ Component Architecture
- [ ] Reusable button components
- [ ] Form input components
- [ ] Modal/dialog components  
- [ ] Loading state components
- [ ] Alert/notification components

### ✅ Responsive Design
- [ ] Mobile-first approach (320px+)
- [ ] Tablet breakpoint (768px+)
- [ ] Desktop breakpoint (1024px+)
- [ ] Large screen optimization (1280px+)

### ✅ CSS Framework Setup (styles.html)
- [ ] Tailwind CSS integration
- [ ] Custom CSS variables
- [ ] Component-specific styles
- [ ] Animation utilities
- [ ] Print styles

## Detailed File Specifications

### index.html
**Requirement:** Create the main HTML template file with the following structure:
- Semantic HTML5 document with proper meta tags and viewport
- Include styles using Google Apps Script include() function
- Navigation header with app title, user email display, and admin toggle button (conditional)
- Main content area using CSS Grid layout (4 columns on large screens)
- Sidebar section (1 column) containing search input and "Add IP Address" button
- Main data area (3 columns) with content header and data container for dynamic content
- Loading state with animated spinner
- Container elements for modals and toast notifications
- Include JavaScript using include() function
- Use Tailwind CSS classes for styling throughout
- Implement responsive design that works on mobile and desktop

### styles.html
**Requirement:** Create the CSS stylesheet file that includes:
- Tailwind CSS CDN integration with custom configuration
- Configure Tailwind with extended primary color palette (blue shades)
- Define reusable component classes using @apply directive:
  - Button variants: .btn-primary, .btn-secondary, .btn-danger with hover/focus states
  - Form components: .form-input, .form-label with consistent styling
  - Table components: .table-row with hover effects
  - Modal components: .modal-overlay, .modal-content with proper z-index
  - Toast notification classes with color-coded borders
  - Loading state classes including skeleton animations
  - Focus management classes for accessibility
  - Print-specific styles that hide non-essential elements
- Include transition animations for smooth interactions
- Ensure all interactive elements have proper focus indicators
- Use CSS custom properties where beneficial for consistency

## Layout Specifications

### Header Layout
- **Height:** 64px (h-16)
- **Background:** White with subtle shadow
- **Content:** Max-width container with flex layout
- **Logo:** Left-aligned, bold text
- **User info:** Right-aligned with admin toggle

### Sidebar Layout  
- **Width:** 25% on desktop, full-width on mobile
- **Background:** White card with shadow
- **Content:** Search, filters, primary actions
- **Responsive:** Collapsible on mobile

### Main Content Area
- **Width:** 75% on desktop, full-width on mobile
- **Background:** White card with shadow  
- **Layout:** Table/grid for data display
- **Responsive:** Horizontal scroll for tables

### Modal Specifications
- **Backdrop:** Semi-transparent gray overlay
- **Content:** Centered white card with shadow
- **Width:** Max 500px, responsive padding
- **Animation:** Fade in/out transitions

## Accessibility Requirements

### WCAG 2.1 Compliance
- [ ] Color contrast ratio minimum 4.5:1
- [ ] Keyboard navigation support
- [ ] Screen reader compatibility
- [ ] Focus indicators on all interactive elements
- [ ] Semantic HTML structure
- [ ] Alt text for images/icons
- [ ] Form labels properly associated

### Interactive Elements
- [ ] All buttons have accessible names
- [ ] Form inputs have labels or aria-label
- [ ] Modal focus management
- [ ] Skip navigation links
- [ ] Error states announced to screen readers

## Performance Requirements

### Loading Optimization
- [ ] Critical CSS inlined
- [ ] Non-critical CSS loaded async
- [ ] Images optimized and lazy-loaded
- [ ] JavaScript modules loaded efficiently

### Responsive Performance
- [ ] Mobile-first CSS approach
- [ ] Efficient media queries
- [ ] Touch-friendly interactive elements (44px minimum)
- [ ] Fast hover states on desktop only

## Definition of Done
- ✅ Complete HTML structure with semantic markup
- ✅ Tailwind CSS properly integrated and configured
- ✅ Responsive design works across all breakpoints
- ✅ Navigation and layout components functional
- ✅ Accessibility standards met (WCAG 2.1 Level AA)
- ✅ Performance optimization implemented
- ✅ Cross-browser compatibility verified
- ✅ Print styles working correctly
- ✅ Component architecture ready for UI development
- ✅ Loading states and transitions smooth

## Next Steps  
Once frontend foundation is complete, move to Ticket #4 for IP management UI components.