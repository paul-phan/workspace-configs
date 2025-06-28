# Saved Memories

This file contains all saved memories from previous conversations, organized by topic area.

## Development Workflow & Testing

### Testing Practices
When testing code changes or integrations, avoid automatically creating test files unless specifically requested by the user. Always clean up any test files immediately after testing. The user prefers to keep the codebase clean without temporary test artifacts. Instead, focus on using existing test suites or minimal, temporary testing approaches that don't leave files behind.

### Code Quality
After completing any substantial coding task in the builder project, always run `nr biome:fix` to format and lint all code changes. This ensures consistent code formatting and catches any linting issues. The command will auto-fix most formatting problems and provide feedback on any remaining issues that need manual attention.

## Project Structure & Configuration

### Project Directories
The `/Users/paul/Workspace/weaverse-project/workspace-configs` directory is specifically for user configuration files, not the actual working codebase. When referencing project directories for interactive feedback: Builder project uses `/Users/paul/Workspace/weaverse-project/builder`, Weaverse monorepo uses `/Users/paul/Workspace/weaverse-project/weaverse`, and workspace configs go to `/Users/paul/Workspace/weaverse-project/workspace-configs` (which is for user configuration storage, not active development work).

### Agent Project
The project is run inside a theme project using the CLI command 'npx @weaverse/agent'. Paths should not be hard-coded because they start from the theme root when the command is executed.

The @weaverse/agent project is a WebSocket server that creates a bridge between Weaverse Studio (web-based) and local Hydrogen theme development. Key architecture: 1) Runs on port 9797 via `npx @weaverse/agent` in theme folder, 2) Requires WEAVERSE_PROJECT_ID environment variable, 3) Handles two main message types: `prepareDataForGeneration` (gathers context including package.json dependencies, latest 3 section files from app/sections/, project structure) and `addFiles` (receives generated code from Weaverse AI and writes securely to local filesystem), 4) Uses SectionFilesService to auto-detect section directories and provide existing code patterns as AI context, 5) Implements security whitelist for safe file operations, 6) Workflow: developer runs agent → Studio connects via WebSocket → AI gets project context → developer prompts for section creation → AI generates code based on context → agent writes files locally. This enables seamless AI-powered code generation directly into local development environment. (Can be update in the future))

## Code Linting & Formatting

### Biome Configuration
When setting up biome linting for projects, create a project-specific biome.json file and migrate to v2.0.0 using "npx @biomejs/biome migrate --write". Key changes in v2.0.0: schema updated to "https://biomejs.dev/schemas/2.0.0/schema.json", organizeImports moved to top level with "enabled": true (not under assists/actions), formatter uses "ignore" array (not "include" with negations), files section uses separate "include" and "ignore" arrays. Essential rules for Node.js/TypeScript: "useNodejsImportProtocol": "error", "noInferrableTypes": "error", "noExplicitAny": "warn". Use npm scripts: "biome:check" and "biome:fix" for workflow integration.

### Fallback Commands
For projects without Biome configuration, use `npx @biomejs/biome format --write` as a fallback command. This applies default Biome formatting without requiring a biome.json config file. The command hierarchy is: 1) nr biome:fix (preferred), 2) npx biome check --write (for configured projects), 3) npx @biomejs/biome format --write (for unconfigured projects). This ensures consistent formatting across all project types.

## AI Model Configuration

### Model Preferences
User prefers using the claude-4-sonnet model by default for future tasks, but when the conversation context is large, prefers using openrouter to avoid hitting rate limits on their Claude API.

### AI Development Mode
When in AI dev mode and the user requests code generation, use the vercelModel for initial code generation with @image-to-section.md as the prompt, then run through the default model (Claude) for a complete result, ensuring strict adherence to Weaverse component structure and best practices.

## Interactive Feedback System

### Enhanced Feedback Tool
The builder project now uses mcp-feedback-enhanced. Update the Interactive Feedback Rule: Use `mcp_mcp-feedback-enhanced_interactive_feedback` instead of the old `mcp_interactive-feedback-mcp_interactive_feedback`. The tool parameters remain the same (project_directory, summary, timeout). The enhanced version has auto-approval configured for interactive_feedback calls. All other aspects of the Interactive Feedback Rule remain unchanged - still call when needing clarification, always before completion, include memory-saving prompts, and use the same project directory detection patterns.

## React & State Management

### Builder Project Tech Stack
The builder project uses React Router v7 (not Remix) and React 19 with React Compiler enabled. Key implications: 1) No need for useMemo/useCallback optimizations as React Compiler handles them automatically, 2) Use React Router v7 patterns and imports (from 'react-router', not 'react-router-dom'), 3) Route type imports from './+types/[routeName]', 4) Modern React 19 features available, 5) Performance optimizations handled by compiler. When writing code, avoid manual memoization and use React Router v7 conventions.

### Preact Signals
The Weaverse builder project uses Preact Signals for state management, located in the app/stores/ directory. Key patterns: 1) Import signal and batch from '@preact/signals-react', 2) State organized in /signal/, /context/, /subscription/, and /rpc/ subdirectories, 3) Signal declarations like `let pageSignal = signal<PageSignal>({...})`, 4) Access values with `.value` property, 5) Use batch() for multiple updates, 6) Has @preact/signals-react-transform for optimization. Examples: selectedSignal, pageSignal, previewSignal, themeConfigSignal. Legacy state uses global-state-hook in /subscription/ directory. This reactive state system provides fine-grained reactivity across the editor interface.

## CSS & Styling

### Tailwind CSS v4
The Weaverse builder project uses Tailwind CSS v4. Key differences from earlier versions: 1) Important modifiers must be placed at the beginning of class names (!className, not className!), 2) New syntax features and configuration options, 3) When writing or fixing CSS classes, always follow Tailwind CSS v4 conventions and syntax rules. Always validate class syntax against v4 requirements when working with Tailwind classes in this project.

## Consent Management

### Custom Consent System
Implemented complete custom consent management system with styled banner and dialog. Features: 1) Non-blocking banner at bottom with project's Button components and design tokens, 2) Custom modal dialog with individual toggles for each consent purpose, 3) Proper state management without useMemo (React 19 compatible), 4) Full API integration with /api/public/consent endpoint, 5) Responsive design with proper spacing and typography, 6) Dark mode support, 7) Accessibility with proper labels and focus management. Key components: CustomConsentBanner (bottom banner), CustomConsentDialog (modal with toggles), ConsentManagerProvider wrapper. Styling uses project's info/primary colors, shadows, and button variants. Dialog has improved UX with larger size (max-w-lg), better spacing, styled checkboxes, and border separator for actions.

### Cross-Subdomain Implementation
Implemented cross-subdomain cookie consent sharing for Weaverse properties (weaverse.io, studio.weaverse.io). Key components: 1) ConsentProvider wraps entire app with c15t ConsentManagerProvider context, 2) Cross-subdomain cookie utilities with domain=.weaverse.io for session ID and consent choice, 3) Server-side cookie setting in consent API responses, 4) Automatic localStorage to cookie migration, 5) Fallback handling for localhost and edge cases. Benefits: single consent choice across all subdomains, seamless user experience, GDPR compliant with database audit trail. Fixed ConsentManagerProvider context warnings by proper provider hierarchy.

### Consent Management Patterns
When implementing consent management with React Router v7 and database persistence, follow this pattern: 1) Use saveConsentWithBody() to avoid "Body is unusable" errors by passing parsed body instead of re-reading request, 2) Always use async/await for consent submission and only call revalidator.revalidate() AFTER successful database save to ensure fresh data, 3) Add proper error handling - skip revalidation on API errors to prevent stale data, 4) Use deferred data loading in root loader for non-blocking consent initialization, 5) Add biome-ignore comments for useEffect dependencies that would cause infinite loops (consentManager.consents), 6) Clean up debug console.log statements in production code while keeping essential error logging. This ensures reliable consent state management with proper data consistency across the app.

### Simplified Configuration
For consent management in the builder project, we successfully simplified by hardcoding consent purposes and vendors instead of storing them in database. Key pattern: 1) Create `/utils/consent-config.ts` with static CONSENT_PURPOSES and CONSENT_VENDORS arrays, 2) Remove ConsentPurpose and ConsentVendor database models, 3) Keep only ConsentRecord and ConsentSettings in database, 4) Update API routes to return hardcoded purposes via `?action=purposes`, 5) Add c15t packages to Vite SSR noExternal config to fix CSS loading issues. This reduces database complexity while maintaining full GDPR compliance and easier maintenance.

### Context Provider Issues
Fixed "useConsentManager must be used within a ConsentManagerProvider" error by: 1) Creating ThirdPartyInitializer component that wraps use3rdStuff calls inside ConsentProvider, 2) Moving use3rdStuff calls from layout components into ThirdPartyInitializer, 3) Adding try/catch to useConsent hook to gracefully handle missing ConsentManagerProvider, 4) Adding ConsentProvider to both _default.tsx and _landing.tsx layouts, 5) Adding c15t packages to Vite SSR noExternal config. The key insight: React hooks must be called inside their provider context, so third-party initialization must happen inside ConsentProvider wrapper.

### Script Loading Optimization
When implementing consent-based third-party script loading, avoid infinite loops by: 1) Only include changing values in useEffect dependencies (consent states), exclude server-determined flags like isBot, 2) Use useRef for stable data access (appDataRef.current) to avoid object dependencies, 3) Memoize consent states with useMemo based on consent functions, 4) Keep dependencies minimal - only track what actually triggers re-execution. Pattern: const consentStates = useMemo(() => ({ analytics: hasAnalyticsConsent() }), [hasAnalyticsConsent]); useEffect(() => { /* use appDataRef.current */ }, [consentStates.analytics])

## Error Handling

### Robust Error Types
Replace brittle string matching error handling with proper error type checking. Pattern: 1) Create custom error classes (ConsentValidationError, ConsentNotFoundError) with proper names, 2) Import ZodError from 'zod' for validation errors, 3) Use instanceof checks instead of error.message.includes(), 4) Throw custom errors from server functions and re-throw ZodErrors, 5) Handle Prisma errors by checking error.code (P2025 = not found), 6) Provide detailed error responses with proper HTTP status codes (400 for validation, 404 for not found, 500 for server errors). This makes error handling more reliable and maintainable.

## Database & Prisma

### PlanetScale Commands
For hosted databases like PlanetScale in the builder project, always use `npx prisma db push` instead of `npx prisma migrate dev`. PlanetScale doesn't support shadow databases which are required for migrations, causing errors like "VT12001: unsupported: create database by failDBDDL". The db push command applies schema changes directly without requiring migration files or shadow databases.

### Referential Actions
When implementing consent management with Prisma, always add referential actions to relations with Shop model. Use onDelete: Cascade for ConsentRecord and ConsentSettings relations since consent data is meaningless without the associated shop. This ensures GDPR compliance by automatically cleaning up consent data when shops are deleted, preventing orphaned records. Pattern: shop Shop? @relation(fields: [weaverseShopId], references: [id], onDelete: Cascade)

## Date Handling

### Hydration Safety
When using dates in JSX that could cause hydration mismatches (like `new Date().toLocaleDateString()`), always compute them on the server side in a loader function instead of directly in JSX. Server and client can have different timezones/times causing React hydration errors. Pattern: 1) Add loader function, 2) Compute date server-side: `return { lastUpdated: new Date().toLocaleDateString() }`, 3) Use `useLoaderData()` in component, 4) Render the pre-computed value: `{lastUpdated}`. This ensures consistent rendering between server and client.

## Package Management & Releases

### Changeset Process
The changeset release process for @weaverse packages follows this sequence: 1) Navigate to /Users/paul/Workspace/weaverse-project/weaverse (the monorepo root), 2) Run `npm run changeset` to create a changeset, 3) Run `npm run changeset version` to apply changesets and update versions, 4) Run `npm run changeset publish` to publish packages. If authentication issues occur with changeset publish, individual packages can be published directly from their directories using `npm publish`. The changeset system handles version bumping and dependency updates across the monorepo automatically.

## Zod & Schema Validation

### Zod v4 Upgrade
Successfully upgraded @weaverse/schema from Zod v3 to Zod v4 with significant benefits: 1) Updated package.json to use `zod@^3.25.0` and import from `zod/v4` path, 2) Fixed function validation issues by simplifying to `z.function()` without args/returns chaining, 3) Enhanced error handling by removing custom error functions that were incompatible, 4) Re-exported `z` from index.ts for backward compatibility, 5) Performance gains: 14x faster string parsing, 7x faster arrays, 6.5x faster objects, 2x smaller bundle, 6) Added comprehensive tests verifying Zod v4 features work correctly, 7) Key challenges: function schema syntax changes, error handling API changes, maintaining backward compatibility while leveraging new features.

### Enhanced Component Registration
Enhanced the original registerComponent function in WeaverseHydrogenRoot.tsx to utilize Zod v4 validation from @weaverse/schema with Cloudflare Workers compatibility. Key improvements: 1) Added validateComponentSimple() call during component registration in development mode, 2) Provides detailed console feedback with ✅ success or ⚠️ warning messages, 3) Clarified that loader is optional in both HydrogenElement and HydrogenComponent interfaces with TypeScript comments, 4) Registration continues even with validation warnings (graceful degradation), 5) Zero performance impact in production as validation only runs in development, 6) CF Workers compatible development detection using globalThis.__DEV__ or localhost hostname checks instead of process.env.NODE_ENV, 7) Imports validateComponentSimple from '@weaverse/schema' package. This ensures all component registrations are validated against the ElementSchema using the same Zod v4 system across the entire Weaverse ecosystem while working in CF Workers environment.

## Documentation & MDX

### MDX Code Examples
When writing MDX documentation files that contain code examples with exports (like React components or schema definitions), always wrap them in proper markdown code blocks with language tags (```tsx, ```typescript, ```jsx, etc.). Never use raw code outside of code blocks or custom HTML-like tags (<example>, <query>, etc.) as these cause MDX bundler to treat examples as executable JavaScript, leading to "Multiple exports with the same name" errors. Key rules: 1) Use ```tsx for React/TypeScript examples, 2) Use ```typescript for TypeScript-only code, 3) Use ```graphql for GraphQL queries, 4) Remove any custom XML-like wrapper tags, 5) Ensure all export statements are inside code blocks to prevent execution conflicts.

## Weaverse Schema System

### createSchema() Function
For all new Weaverse component schemas, always use the createSchema() function instead of manual HydrogenComponentSchema type annotations. Import createSchema from @weaverse/hydrogen and export with: export let schema = createSchema({ ... }). The old HydrogenComponentSchema is deprecated but maintained for backward compatibility. This provides better TypeScript inference, runtime validation, and consistency across components.

Weaverse has modernized component schema definitions to use the createSchema() function instead of manual HydrogenComponentSchema type annotations. Key changes: 1) Import createSchema from @weaverse/hydrogen, 2) Replace "export const schema: HydrogenComponentSchema = {}" with "export let schema = createSchema({})", 3) Benefits include runtime validation via Zod, better TypeScript inference, consistent validation, and early error detection. Updated 14 documentation files including guides, API docs, tutorials, and AI prompts. The old HydrogenComponentSchema interface is deprecated but maintained for backward compatibility.

The modern and recommended way to define component schemas in Weaverse is by using the createSchema() function. All documentation and code should use createSchema() instead of manual HydrogenComponentSchema type definitions.

### Type System Architecture
Optimized Weaverse type system follows hierarchy: core → react → schema|hydrogen. Schema package is the source of truth for all component schema types using Zod. Types are inferred using z.infer<> for consistency. Hydrogen extends schema types rather than redefining them. Key imports: import type { BasicInput, HeadingInput, InspectorGroup, InputType, PageType, SchemaType, ComponentPresets } from '@weaverse/schema'. Hydrogen re-exports these for backward compatibility. Benefits: single source of truth, runtime validation matches types, reduced duplication.

### Schema Field Updates
In Weaverse component schemas, the "inspector" field is deprecated and should be replaced with "settings". Both fields have the same structure (array of InspectorGroup), but "settings" is the preferred naming going forward. When working with component schemas, use "settings" instead of "inspector" for new components and consider migrating existing components.
