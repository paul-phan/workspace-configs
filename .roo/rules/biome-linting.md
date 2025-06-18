---
description: 
globs: 
alwaysApply: true
---
# Biome Linting Rule - Configuration Detection and Auto-Fix

## Overview
This rule guides Roo Code on how to handle Biome linting problems by following the nearest `biome.json` configuration and using available fix commands.

## Configuration Detection Priority

### 1. **Find Nearest biome.json**
When encountering linting issues, always follow this hierarchy:
1. Current file's directory and parent directories for `biome.json`
2. Project root `biome.json` (builder/, weaverse/, etc.)
3. Shared configuration from `@weaverse/biome` package

### 2. **Project-Specific Configurations**

**Builder Project (`/Users/paul/Workspace/weaverse-project/workspace/builder`):**
- Extends: `@weaverse/biome/biome.json`
- Special exclusions: `prisma/`, `public/`, `build/`, specific utility files
- Custom rules: `noExplicitAny: "off"`

**Weaverse Project (`/Users/paul/Workspace/weaverse-project/workspace/weaverse`):**
- Extends: `./packages/biome/biome.json`
- Excludes: `dist/`, `build/`, `.turbo/`, `templates/`
- VCS integration enabled

**Shared Base Configuration (`weaverse/packages/biome/biome.json`):**
- `useConst: "off"` - Use `let` by default
- A11y rules: mostly warnings, some disabled
- Formatting: single quotes, semicolons as needed, space indentation
- Security: `noDangerouslySetInnerHtml: "off"`

## Auto-Fix Commands

### 1. **Primary Fix Command**
```bash
npm run biome:fix
```
**Available in all projects:**
- Builder: `biome check --write --diagnostic-level=error`
- Weaverse: `biome check --write --diagnostic-level=error`
- Pilot template: `biome check --write --diagnostic-level=error`

### 2. **Manual Biome Commands**
```bash
# Check without fixing
npx biome check

# Fix with write
npx biome check --write

# Check specific files
npx biome check --write ./path/to/file.ts

# Format only
npx biome format --write ./path/to/file.ts
```

## Linting Problem Resolution Workflow

### 1. **When Roo Code Encounters Linting Issues:**

**✅ DO:**
- Identify the nearest `biome.json` configuration
- Check if `npm run biome:fix` is available in `package.json`
- Suggest running the fix command before manual corrections
- Follow the detected Biome configuration rules
- Respect project-specific overrides (like `noExplicitAny: "off"` in builder)

**❌ DON'T:**
- Auto-fix linting errors without user consent
- Ignore the nearest Biome configuration
- Use generic linting rules instead of Biome-specific ones
- Modify Biome configuration files without explicit request

### 2. **Step-by-Step Resolution:**

```typescript
// 1. Detect configuration
// Look for biome.json in current directory and parents

// 2. Suggest fix command
// "I found Biome linting issues. Would you like me to run `npm run biome:fix`?"

// 3. Apply configuration-aware fixes
// Follow the detected biome.json rules for any manual fixes

// 4. Verify compliance
// Ensure fixes align with project-specific Biome settings
```

## Configuration-Specific Guidelines

### **Formatting Rules (All Projects):**
- **Quotes:** Single quotes (`'`) preferred
- **Semicolons:** As needed (not always required)
- **Indentation:** Spaces (not tabs)
- **Line length:** Follow Biome defaults

### **Linting Rules (Project-Specific):**

**Builder Project:**
- `any` type allowed (`noExplicitAny: "off"`)
- Standard accessibility warnings
- Exclude Prisma generated files

**Weaverse Projects:**
- `let` preferred over `const` (`useConst: "off"`)
- Accessibility rules as warnings
- `dangerouslySetInnerHTML` allowed for React components
- Hook usage warnings enabled

**Shared Base Rules:**
- Array index keys: warn (not error)
- Assignment in expressions: warn
- Implicit any: warn
- Hook at top level: warn

## Integration with Development Workflow

### **Before Code Changes:**
1. Check if `npm run biome:fix` exists
2. Suggest running it for automatic fixes
3. Only proceed with manual fixes if auto-fix isn't sufficient

### **During Code Writing:**
- Follow the detected Biome configuration
- Respect project-specific rule overrides
- Use consistent formatting according to nearest config

### **Error Messages:**
When suggesting fixes, reference the specific Biome rule and configuration:
```
"This violates the Biome rule 'suspicious/noArrayIndexKey' (set to 'warn' in your biome.json). 
Would you like me to run 'npm run biome:fix' to auto-correct this and other issues?"
```

## Commands to Suggest

### **Primary Commands:**
```bash
# Auto-fix all issues
npm run biome:fix

# Check only (no fixes)
npm run biome:check  # if available

# Manual biome commands as fallback
npx biome check --write
```

### **Project Detection:**
```bash
# Find nearest biome.json
find . -name "biome.json" -not -path "*/node_modules/*" | head -1

# Check if fix command exists
grep -q "biome:fix" package.json && echo "npm run biome:fix available"
```

## Best Practices

1. **Always check for `npm run biome:fix` availability first**
2. **Respect the nearest `biome.json` configuration hierarchy**
3. **Suggest auto-fix before manual corrections**
4. **Maintain consistency with project-specific rule overrides**
5. **Don't auto-fix without user permission**
6. **Provide clear explanations referencing Biome rules and config**

## Integration with Other Rules

- **Follows user-specific rule:** "Never auto-fix linter errors. Notify the user; fix only when expressly asked."
- **Works with TypeScript rules:** Handle both Biome linting and TypeScript errors appropriately
- **Respects project structure:** Uses appropriate project directories from interactive feedback rule
