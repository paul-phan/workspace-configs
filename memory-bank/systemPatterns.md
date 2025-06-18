# SYSTEM PATTERNS: Weaverse Agent

## ARCHITECTURAL PATTERNS

### Event-Driven Architecture
- **WebSocket Events**: Server responds to client connection and message events
- **Message Types**: Discrete message handlers for different operation types
- **Asynchronous Processing**: Non-blocking message handling with async/await
- **Event Lifecycle**: Connection → Authentication → Message Processing → Response

### Schema-First Design
- **Zod Validation**: All data structures defined with runtime-validated schemas
- **Type Safety**: TypeScript types inferred from Zod schemas
- **Input Validation**: All incoming data validated before processing
- **Error Handling**: Schema validation errors handled gracefully

### Modular Handler Pattern
```typescript
// Pattern: Separate handlers for different message types
export function handleGenerationRequest(ws: WebSocket, data: any): void
export async function handleAddFiles(ws: WebSocket, message: any): Promise<void>
export function sendErrorResponse(ws: WebSocket, message: string): void
```

### Environment Configuration Pattern
- **Validation on Startup**: Environment checked before server initialization
- **Required vs Optional**: Clear distinction between required and optional variables
- **Graceful Failure**: Exit with clear error message if required variables missing
- **Client Communication**: Environment data sent to clients on connection

## FILE SYSTEM PATTERNS

### Safe File Operations
```typescript
// Pattern: Ensure directory exists before writing file
await ensureExistDirectory(directory);
fs.writeFileSync(path.join(directory, fileName), validatedFile.content);
```

### Cross-Platform Path Handling
- **Path Module**: Uses Node.js `path` module for platform-agnostic paths
- **Path Splitting**: Handles both forward slash and backslash separators
- **Directory Creation**: Recursive directory creation with error handling

### Atomic File Operations
- **Write Safety**: Files written atomically to prevent corruption
- **Validation First**: Data validated before any file system operations
- **Error Recovery**: Failed operations don't leave partial files

## COMMUNICATION PATTERNS

### WebSocket Message Protocol
```typescript
// Pattern: Structured message format
{
  type: string,           // Message type identifier
  data?: any             // Optional payload
}
```

### Response Patterns
- **Success Response**: Standard format for successful operations
- **Error Response**: Consistent error message structure
- **Status Updates**: Progress notifications for long-running operations

### Client-Server Handshake
1. Client connects to WebSocket server
2. Server sends environment data immediately
3. Client can then send operation requests
4. Server processes and responds with results

## ERROR HANDLING PATTERNS

### Layered Error Handling
```typescript
// Pattern: Multiple levels of error handling
try {
  const validatedMessage = AddFilesMessageSchema.parse(message);
  // Process validated message
} catch (error) {
  // Send structured error response
  ws.send(JSON.stringify({
    type: "error",
    message: `Failed to add files: ${error}`
  }));
}
```

### Graceful Degradation
- **Connection Errors**: Server continues running if client disconnects
- **Validation Errors**: Invalid messages rejected without affecting other operations
- **File System Errors**: Individual file operation failures don't stop processing

## SECURITY PATTERNS

### Input Sanitization
- **Schema Validation**: All inputs validated against defined schemas
- **Path Validation**: File paths checked to prevent directory traversal
- **Type Safety**: TypeScript prevents type-related security issues

### Principle of Least Privilege
- **Localhost Only**: Server binds only to localhost interface
- **File System**: Only creates files in current working directory and subdirectories
- **Environment Variables**: Only reads explicitly defined environment variables

## BUILD AND DEPLOYMENT PATTERNS

### Multi-Stage Build
1. **Development**: TypeScript with file watching for rapid iteration
2. **Build**: Compilation to JavaScript with minification
3. **Distribution**: NPM package with binary executable

### Dependency Management
- **External Dependencies**: Core libraries (ws, zod, dotenv) marked as external
- **Type Definitions**: Separate development dependencies for type checking
- **Runtime Optimization**: Minimal bundle size for production

### Package Structure
```
@weaverse/agent/
├── build/           # Compiled JavaScript
│   └── index.js     # Main executable
├── package.json     # Package metadata
└── README.md        # Documentation
```

## MONITORING AND OBSERVABILITY PATTERNS

### Structured Logging
- **Connection Events**: Client connect/disconnect logging
- **Operation Logging**: Message type and processing status
- **Error Logging**: Detailed error information with context

### Debug Information
- **Development Mode**: Verbose logging during development
- **Production Mode**: Essential logging only
- **Performance Tracking**: Build and operation timing information

## EXTENSIBILITY PATTERNS

### Plugin Architecture Readiness
- **Handler Registration**: Easy to add new message type handlers
- **Schema Extension**: Zod schemas can be extended for new message types
- **Middleware Pattern**: WebSocket middleware can be added for cross-cutting concerns

### Configuration Extension
- **Environment Variables**: New configuration options easily added
- **Feature Flags**: Environment-based feature toggles supported
- **Client Capabilities**: Server can adapt behavior based on client capabilities

## INTEGRATION PATTERNS

### Weaverse Builder Integration
- **Standardized Protocol**: Consistent message format with Weaverse Builder
- **Real-time Updates**: Immediate file synchronization capabilities
- **Project Context**: Environment variables provide project-specific context

### Development Tool Integration
- **NPM Scripts**: Standard npm commands for development workflow
- **Binary Distribution**: Easy installation and execution via NPM
- **IDE Support**: TypeScript provides excellent IDE integration and autocomplete 