# AGENTS.md - Opale Dark Theme

This document provides guidance for AI coding agents working in this repository.

## Project Overview

Opale is a Redmine 6.x theme written in SCSS. It provides a dark theme with left sidebar, colored tracker links, Jira-inspired priority icons, and customizable SCSS variables.

## Build Commands

```bash
# Install dependencies
npm install

# Build CSS from SCSS (runs copy, sass, postcss)
npm run build

# Watch for SCSS changes and rebuild automatically
npm run watch
```

## Lint Commands

```bash
# Lint all SCSS files
npm run lint

# Lint SCSS files only
npm run lint:sass

# Lint specific file(s)
npx stylelint src/sass/components/_buttons.scss
npx stylelint "src/sass/components/*.scss"
```

## Test Commands

This project has no automated tests. Manual testing involves:
1. Building the theme with `npm run build`
2. Copying the theme to a Redmine installation's `themes` folder
3. Selecting the theme in Redmine Administration settings

## Pre-commit Hooks

Husky and lint-staged are configured to automatically:
1. Run stylelint on changed SCSS files
2. Build CSS with grunt
3. Stage the output files

## Code Style Guidelines

### Editor Configuration

- End of line: LF
- Charset: UTF-8
- Indent style: 2 spaces
- Trim trailing whitespace: true
- Insert final newline: true

### SCSS Style

#### Imports and Module System

This project uses the modern Sass module system (`@use` and `@forward`), NOT the legacy `@import` syntax.

```scss
// Correct - use @use for modules
@use "sass:color";
@use "sass:map";
@use "sass:math";
@use "../variables";
@use "../mixins/variants";

// Incorrect - do NOT use @import
@import "variables";  // AVOID
```

When using `@use`, access variables/mixins with namespace:

```scss
@use "../variables";

.button {
  padding: variables.$btn-padding-vertical variables.$btn-padding-horizontal;
}
```

#### Property Order (SMACSS)

CSS properties should follow SMACSS ordering:
1. Box (display, position, float, etc.)
2. Border (border, outline)
3. Background
4. Text (font, color, text-align, etc.)
5. Other (animation, transition, etc.)

This is enforced via `stylelint-config-property-sort-order-smacss`.

#### Naming Conventions

- **Files**: Use underscore prefix for partials (e.g., `_buttons.scss`, `_variables.scss`)
- **Variables**: Use kebab-case with `$` prefix (e.g., `$btn-padding-vertical`, `$sidebar-width`)
- **Maps**: Use kebab-case keys in maps (e.g., `$priority-default-icons-map`)
- **Mixins/Functions**: Use kebab-case (e.g., `button-variant`, `parse-length`)
- **Selectors**: Follow Redmine's existing class names

#### Variable Definitions

- All variables should use `!default` flag to allow overriding
- Group related variables with comment headers
- Align variable values for readability

```scss
// Correct
$sidebar-width:                 270px !default;
$sidebar-padding-vertical:      $padding-base-vertical !default;
$sidebar-padding-horizontal:    $padding-side !default;
```

#### Functions

- Place functions in `src/sass/functions/` directory
- Document function purpose with comments

```scss
// Extract the length of margin/padding/border for given side
@function parse-length($value, $side) {
  // ...
}
```

#### Mixins

- Place mixins in `src/sass/mixins/` directory
- Prefix mixin files with underscore (e.g., `_variants.scss`)
- Use meaningful parameter names

```scss
@mixin button-variant($color, $bg, $border) {
  // ...
}
```

#### Comments

- Use `//` for single-line comments (SCSS style, not compiled)
- Use block comments `/* */` for documentation that should appear in output
- Add section headers for major blocks

### File Organization

```
src/sass/
├── _variables.scss      # All theme variables
├── _functions.scss      # Function imports
├── _mixins.scss         # Mixin imports
├── application.scss     # Main entry point
├── functions/           # Custom Sass functions
├── mixins/              # Reusable mixins
├── components/          # Redmine component styles
├── vendor/              # Third-party code (normalize, bootstrap, icons)
└── plugins/             # Plugin-specific styles
```

### Error Handling

- Use `@if` / `@else` for conditional logic
- Use `@error` for invalid function/mixin arguments
- Use `@warn` for deprecation notices

### Browser Support

Target browsers defined in `package.json` browserslist:
- Last 2 versions
- > 1% usage
- Not dead

Autoprefixer is run automatically during build.

## Customization Pattern

Users can override variables by creating `_custom-variables.scss`:

```scss
@use 'variables' with (
  $sidebar-position: right,
  $brand-primary: #614ba6
);
```

## Commit Message Guidelines

- Start with uppercase letter
- Keep first line under 50 characters
- Use present tense verbs: Add, Change, Deprecate, Remove, Fix
- Link issues with keywords (e.g., "fixes gagnieray/opale#50")

Example:
```
Add dark mode support for calendar component
```

## Pull Request Guidelines

- Use clean, concise titles
- Start with: Add, Change, Deprecate, Remove, Fix
- Ensure lint and build pass before submitting
- Target appropriate branch (redmine-6.x or redmine-5.x), not master

## Important Notes

- Never include `node_modules` in production
- The `src/sass/vendor/` directory is excluded from linting
- Output CSS goes to `stylesheets/application.css` and `plugins/` directories
