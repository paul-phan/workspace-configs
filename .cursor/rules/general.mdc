---
description: 
globs: 
alwaysApply: true
---
# Linter
- **Never auto-fix** linter errors.  
- Notify the user; fix only when expressly asked.
- Fix TypeScript errors if they exist, but don't try to fix linting issues.

# Function Size
- Keep each function **≤ 5 lines**.  
- If it grows, extract clear, single-purpose helpers.  
- Clarity beats brevity; use descriptive names, no hidden side effects.

# General Code Style & Formatting
- English only.  
- One export per file; kebab-case filenames.  
- Follow Biome; braces always; semicolons; line length ≤ 120 chars.  
- No commented-out code; pass all lint checks.
- Use `let` by default for variable declarations.

# Naming
- **PascalCase**: classes, enums, types.  
- **camelCase**: variables, functions, methods.  
- **UPPER_SNAKE_CASE**: constants, env vars.  
- Booleans start with *is/has/can/should*.  
- No Hungarian or cryptic abbreviations; keep terminology consistent.

# Functions & Logic
- Single responsibility; aim **< 20 lines** overall.  
- Use early returns to avoid deep nesting.  
- Prefer `map | filter | reduce`; arrows for small callbacks.  
- RO-RO: receive/return objects for multi-param calls.  
- Pure functions where possible; handle errors explicitly.

# Data Handling
- Use `readonly`, `ReadonlyArray` for immutable data structures.  
- Avoid `any`; use strict types, generics, interfaces.  
- Validate external data at boundaries.  
- Handle `null/undefined` with optional chaining or defaults.  
- Minimize global mutable state; clean up resources.
