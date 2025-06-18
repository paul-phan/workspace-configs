# TECHNICAL CONTEXT: Weaverse Agent

## DEVELOPMENT ENVIRONMENT
- **Package Manager**: Bun
- **Runtime**: Node.js compatible with Bun
- **TypeScript**: v5.8.3
- **Target**: ES2021 with ES2022 modules

## ARCHITECTURE OVERVIEW
### WebSocket Server
- **Library**: ws v8.18.2
- **Port**: 9797 (configurable)
- **Protocol**: WebSocket for real-time bidirectional communication
- **Connection Management**: Handles multiple client connections

### Message Handling System
- **Schema Validation**: Zod v3.25.57 for runtime type safety
- **Message Types**: 
  - `prepareForAIGeneration`: Handles AI generation requests
  - `addFiles`: Processes file addition requests
  - `environment`: Sends environment data to clients

### File System Operations
- **File Writing**: Atomic file operations with directory creation
- **Path Handling**: Cross-platform path resolution
- **Validation**: Schema-based validation before file operations

## ENVIRONMENT CONFIGURATION
### Required Environment Variables
- `WEAVERSE_PROJECT_ID`: Required - Identifies the target Weaverse project
- `WEAVERSE_API_KEY`: Optional - API authentication key
- `PUBLIC_STORE_DOMAIN`: Optional - Shopify store domain
- `PUBLIC_STOREFRONT_API_TOKEN`: Optional - Shopify storefront API token

### Environment Validation
- Validates required environment variables on startup
- Graceful exit with error message if required variables missing
- Sends environment data to connected clients

## BUILD SYSTEM
### Compilation
- **Bun Build**: Bundles TypeScript to JavaScript
- **Target**: Node.js runtime
- **Output**: `build/` directory
- **Minification**: Enabled for production builds
- **Externals**: dotenv, ws, zod (not bundled)

### Scripts
- `dev`: Development mode with file watching
- `build`: Production build
- `start`: Run compiled JavaScript
- `prepare`: Pre-publication build

## DEPENDENCY MANAGEMENT
### Core Dependencies
- `dotenv`: Environment variable loading
- `ws`: WebSocket server implementation
- `zod`: Runtime type validation

### Development Dependencies
- `@types/node`: Node.js type definitions
- `@types/ws`: WebSocket type definitions
- `typescript`: TypeScript compiler
- `tsx`: TypeScript execution for development

## CROSS-PLATFORM COMPATIBILITY
### File System
- Uses Node.js path module for cross-platform path handling
- Supports both Windows backslash and Unix forward slash paths
- Recursive directory creation for nested paths

### Command Line
- Shebang support for Unix-like systems
- Windows PowerShell compatible
- Cross-platform environment variable access

## ERROR HANDLING
### WebSocket Errors
- Connection error handling with logging
- Message parsing error recovery
- Graceful client disconnection

### File System Errors
- Directory creation failure handling
- File write permission errors
- Path resolution failures

### Schema Validation Errors
- Zod schema validation with detailed error messages
- Invalid message type handling
- Malformed data recovery

## SECURITY CONSIDERATIONS
### Input Validation
- All incoming messages validated against Zod schemas
- File path validation to prevent directory traversal
- Environment variable sanitization

### Network Security
- WebSocket server bound to localhost only
- No external network exposure by default
- Client authentication through environment variables

## LOGGING AND MONITORING
### Console Logging
- Connection status logging
- Error message logging with context
- Startup confirmation with formatted output

### Debug Information
- Message type logging for debugging
- Build process status reporting
- Environment validation results

## PACKAGE DISTRIBUTION
### NPM Package
- **Name**: `@weaverse/agent`
- **Version**: 0.0.2
- **License**: UNLICENSED (private package)
- **Binary**: `weaverse-agent` command

### Publication
- Pre-publication build process
- Includes only `build/` directory in distribution
- Binary executable configuration 