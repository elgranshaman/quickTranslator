# Agent Guidelines for quickTranslator

## Project Overview
Single-file HTML/CSS/JavaScript Thai-English translator web application using OpenTyphoon API. This is a static web application that runs entirely in the browser with no build step or server-side processing.

## Build/Test/Lint Commands
**No build, test, or lint commands exist.** This is a static HTML file that runs directly in a browser.

- **No package.json** - No npm/node dependencies
- **No build tools** - No webpack, vite, or bundlers
- **No testing framework** - No jest, vitest, or test runners configured
- **No linting** - No ESLint, Prettier, or code formatters configured

### To Run the Application
Simply open `index.html` in a web browser:
```bash
# On macOS
open index.html

# On Windows
start index.html

# On Linux
xdg-open index.html
```

No server required - runs entirely client-side.

## Code Style Guidelines

### File Structure
- **Single-file architecture**: All HTML, CSS, and JavaScript in `index.html`
- Document order: `<head>` (meta, title, styles) → `<body>` (DOM elements, script)
- Place CSS in `<style>` block within `<head>` (after meta tags, before title)
- Place JavaScript in `<script>` block at end of `<body>` (just before closing `</body>`)
- No external files or modules - everything self-contained

### HTML Conventions
- Use semantic HTML5 elements (`<div>`, `<button>`, `<textarea>`, `<label>`, etc.)
- IDs for element selection: camelCase (`inputText`, `translateBtn`, `copyBtn`)
- Use descriptive placeholder text that indicates expected input
- Include title attributes on buttons for accessibility tooltips
- Keep HTML structure flat when possible (minimal nesting for performance)
- Use double quotes for attributes: `id="inputText"`

### CSS Conventions
- Use kebab-case for class names (`language-toggle`, `text-box`, `translate-btn`)
- Use 4 spaces for indentation consistently
- Use hex color codes from the established palette:
  - Background: `#1a1a2e` (dark), `#ffffff` (white), `#f8fafc` (light gray)
  - Primary: `#0f172a` (dark blue), `#1e293b` (slate)
  - Error: `#fee` (background), `#c33` (text)
  - Success: `#22c55e` (green)
  - Borders: `#cbd5e1` (light), `#e2e8f0` (lighter)
  - Text: `#1e293b` (dark), `#64748b` (muted), `#475569` (labels)
- Use transitions for interactive elements (0.2s duration standard)
- Responsive design with `@media` queries (mobile breakpoint at 768px)
- Flexbox and CSS Grid for layouts (avoid float-based layouts)
- Define hover, active, and disabled states for all interactive elements
- Use `box-sizing: border-box` globally

### JavaScript Conventions
- Use `const` by default, `let` only when reassignment is necessary
- camelCase for all variable and function names (`apiKeyInput`, `translate()`, `showError()`)
- Use `async/await` for all asynchronous operations (avoid raw Promises with .then())
- Arrow functions for callbacks and event handlers: `(e) => { ... }`
- Constants in UPPER_SNAKE_CASE at top of script (e.g., `API_URL`, `MODEL`, `MAX_TOKENS`)
- Query DOM elements once at script start using `getElementById()`
- Store application state in module-level variables (`isThaiToEnglish`, `translationHistory`)
- Use template literals for string interpolation: `` `Bearer ${apiKey}` ``
- Prefer array methods (map, filter, find) over for loops

### Error Handling
- Wrap all async operations in try/catch blocks
- Display user-facing errors via `showError()` function (auto-dismisses after 5 seconds)
- Validate inputs before making API calls (check for empty strings, missing API keys)
- Validate API responses: check `response.ok`, verify `choices` array exists
- Handle clipboard operations with try/catch (may fail in certain contexts)
- Provide actionable error messages that tell users what to do
- Log errors to console for debugging but don't expose sensitive details to users
- Always reset UI state (buttons, loading indicators) in finally blocks

### API Integration
- Use native `fetch()` API for all HTTP requests
- Set required headers: `Content-Type: application/json`, `Authorization: Bearer ${apiKey}`
- Parse JSON safely: `await response.json().catch(() => ({
- Check response status: `if (!response.ok) { ... }`
- Access nested properties with optional chaining: `data.choices?.[0]?.message?.content`
- Include timeout handling for long-running requests
- Validate API key format before making requests (non-empty check)

### Data Persistence
- Use `localStorage` for persistent storage (API keys and translation history)
- Storage keys: `opentyphoon_api_key`, `translation_history`
- Parse saved data with `JSON.parse()` and null checks
- Limit history to 50 items maximum (use slice to keep latest)
- Save to localStorage immediately on data changes
- Handle localStorage errors (quota exceeded, private browsing mode)
- Never store sensitive data beyond API keys (which are user-provided)

### Event Handling
- Use `addEventListener()` for all event bindings (never inline handlers)
- Keyboard shortcuts implemented:
  - **Enter**: Trigger translation
  - **Shift+Enter**: Insert new line in textarea
  - **Escape**: Clear all text and hide errors
  - **Ctrl+Z**: Undo last clear operation
- Use `e.preventDefault()` when overriding default behavior
- Confirm destructive actions with `window.confirm()` before proceeding
- Debounce rapid-fire events if necessary (input events, resize)
- Use event delegation for dynamically created elements (history items)

### Security Best Practices
- Escape HTML to prevent XSS before rendering user content (use `escapeHtml()` function)
- Use password input type for API keys (`type="password"`)
- Never log API keys or sensitive data to console
- Store API keys only in localStorage (client-side only, never transmitted to our servers)
- Validate all user inputs before processing
- Sanitize content before inserting into DOM
- Use HTTPS for API calls (enforced by API provider)

### Naming Patterns
- **DOM elements**: `<element><Purpose>` format
  - Examples: `inputText`, `outputText`, `translateBtn`, `apiKeyInput`
- **CSS classes**: `<component>-<variant>` format
  - Examples: `text-box`, `lang-btn`, `history-item`, `translate-btn`
- **Functions**: `<verb><Noun>` or `<verb>` format
  - Examples: `translate()`, `updateLanguageLabels()`, `saveToHistory()`, `showError()`
- **State variables**: `is<Name>` for booleans, `<name>History` for arrays
  - Examples: `isThaiToEnglish`, `translationHistory`
- **Constants**: `UPPER_SNAKE_CASE`
  - Examples: `API_URL`, `MAX_TOKENS`, `TEMPERATURE`

### Code Organization
Order of code in the script block:
1. Constants (API URLs, model names, settings)
2. DOM element selections (all getElementById calls)
3. State variables (application state)
4. Initialization code (load from localStorage, initial render)
5. Event listeners (all addEventListener calls)
6. Function definitions (organized by feature)
7. Utility functions (escapeHtml, formatters) at the end

### Performance Guidelines
- Minimize DOM queries - cache elements in variables
- Use event delegation for lists with many items
- Debounce expensive operations (search, resize handlers)
- Limit history to 50 items to prevent memory bloat
- Use CSS animations instead of JavaScript when possible
- Lazy load non-critical functionality
- Keep the single-file structure for fast initial load

### Accessibility
- Use semantic HTML elements
- Include title attributes on icon buttons
- Ensure sufficient color contrast (WCAG AA minimum)
- Support keyboard navigation (Tab, Enter, Escape)
- Use ARIA labels where native semantics are insufficient
- Test with screen readers if adding complex interactions

### Adding New Features
When adding features to this codebase:
1. Maintain single-file structure (no external files)
2. Follow existing color scheme and design patterns
3. Add loading states for all async operations
4. Include comprehensive error handling for user interactions
5. Update localStorage usage when adding persistent data
6. Ensure mobile responsiveness (test at 768px breakpoint)
7. Add keyboard shortcuts for power users
8. Test in multiple browsers (Chrome, Firefox, Safari, Edge)

### Testing (Manual)
Since no automated testing framework exists, perform manual testing:
1. Test translation in both directions (Thai→English, English→Thai)
2. Test with empty inputs and invalid API key (should show errors)
3. Test copy functionality and history features (save, load, clear)
4. Test responsive design at various screen sizes
5. Test keyboard shortcuts
6. Verify localStorage persistence across page reloads
