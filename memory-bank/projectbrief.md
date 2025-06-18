# PROJECT BRIEF: Weaverse Agent

## PROJECT OVERVIEW
**Weaverse Agent** is a WebSocket server that acts as a bridge between the Weaverse Builder and local development environments. It enables real-time file synchronization and communication for seamless development workflow.

## CORE PURPOSE
- **Real-time File Sync**: Automatically receives and applies file updates from Weaverse Builder
- **WebSocket Communication**: Maintains persistent connection with Weaverse Builder for instant updates
- **Project Integration**: Seamlessly integrates with existing Weaverse project structure
- **Development Bridge**: Facilitates communication between cloud-based builder and local development

## CURRENT PROJECT STATE
- Built with TypeScript and Node.js
- Uses Bun as package manager and runtime
- WebSocket server running on port 9797
- Handles file operations and environment validation
- Ready for NPM publication as `@weaverse/agent`

## KEY TECHNOLOGIES
- **Runtime**: Bun
- **Language**: TypeScript 
- **Communication**: WebSocket (ws library)
- **Validation**: Zod schemas
- **Environment**: dotenv for configuration

## PROJECT STRUCTURE
```
agent/
├── src/
│   ├── handlers/message-handlers.ts  # WebSocket message handling
│   ├── index.ts                      # Main server entry point
│   ├── tools.ts                      # Utility functions
│   ├── types.ts                      # TypeScript types and schemas
│   └── utils/environment.ts          # Environment validation
├── build/                            # Compiled output
├── package.json                      # Package configuration
└── tsconfig.json                     # TypeScript configuration
```

## TARGET USERS
- Weaverse theme developers
- Local development environments
- Shopify developers using Weaverse Builder

## SUCCESS CRITERIA
- Stable WebSocket connection with Weaverse Builder
- Reliable file synchronization
- Cross-platform compatibility (Windows, Mac, Linux)
- Easy installation via NPM
- Comprehensive error handling and logging 