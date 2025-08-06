# LearnWorlds JavaScript SDK

## Overview

A TypeScript SDK for integrating with the LearnWorlds API in Node.js backends with full OAuth2 support and comprehensive type safety. This SDK provides full method coverage for the LearnWorlds API v2, including authentication, user management, course access, and enrollment operations.

**Key Features:**
- Full OAuth2 support (Client Credentials, Authorization Code, Resource Owner Password)
- Complete TypeScript type definitions
- Automatic token refresh with persistence callbacks
- Comprehensive error handling with detailed error types
- Support for all LearnWorlds API v2 endpoints
- Modern ES modules with CommonJS compatibility

## Architecture

The SDK is built with a modular architecture:

```
src/
├── client.ts       # Main LearnWorldsClient class with API methods
├── oauth.ts        # OAuth2Manager for authentication flows
├── types.ts        # TypeScript type definitions
├── index.ts        # Main exports
└── __tests__/      # Comprehensive test suite
```

### Core Components

- **LearnWorldsClient**: Main client class that handles API calls and integrates with OAuth2Manager
- **OAuth2Manager**: Dedicated authentication manager supporting all OAuth2 grant types
- **Type System**: Complete TypeScript definitions for all API requests/responses
- **Error Handling**: Custom LearnWorldsError class with detailed error information

### Authentication Architecture

The SDK uses dual-host authentication:
- **OAuth2 endpoints**: `https://yourschool.learnworlds.com` (for token operations)
- **API endpoints**: `https://your-unique-api-host/v2` (for data operations)

## Setup & Installation

```bash
# Install dependencies
pnpm install

# Build the project
pnpm run build

# Run in development mode (watch)
pnpm run dev
```

### Environment Requirements
- Node.js >= 16.0.0
- TypeScript 5.3+
- Modern build tools (tsup for bundling)

## Development Workflow

### Available Scripts

```bash
# Development
pnpm run dev          # Watch mode build
pnpm run build        # Production build
pnpm run clean        # Clean dist folder

# Testing
pnpm run test         # Run tests
pnpm run test:watch   # Run tests in watch mode

# Code Quality
pnpm run lint         # ESLint checking
pnpm run typecheck    # TypeScript type checking

# Publishing
pnpm run prepublishOnly  # Pre-publish build (automatic)
```

### Testing Strategy
- **Unit Tests**: Comprehensive test coverage using Vitest
- **Type Safety**: Full TypeScript checking with strict mode
- **Linting**: ESLint with TypeScript rules

### Release Process
- **Semantic Release**: Automated versioning and changelog generation
- **GitHub Actions**: Automated CI/CD pipeline
- **Dual Format**: Builds both ESM and CommonJS formats

## API Documentation

### Authentication Methods

#### Client Credentials (Server-to-Server)
```typescript
const client = new LearnWorldsClient({
  schoolDomain: 'yourschool',
  clientId: 'your-client-id',
  clientSecret: 'your-client-secret',
  apiHost: 'api.yourschool.learnworlds.com',
});

const tokens = await client.auth.authenticateWithClientCredentials('read_courses');
```

#### Authorization Code Flow (Web Apps)
```typescript
// Step 1: Get authorization URL
const authUrl = client.auth.getAuthorizationUrl('read_user_profile');

// Step 2: Exchange code for token
const tokens = await client.auth.exchangeAuthorizationCode({
  code: 'authorization-code-from-callback',
  redirectUri: 'https://yourapp.com/callback',
});
```

#### Password Flow (Trusted Apps)
```typescript
const tokens = await client.auth.authenticateWithPassword({
  username: 'user@example.com',
  password: 'user-password',
  scope: 'read_user_profile',
});
```

### Core API Methods

#### Course Management
- `getAllCourses(params?: PaginationParams): Promise<Course[]>`
- `getCourse(courseId: string): Promise<Course>`

#### Bundle Management  
- `getAllBundles(params?: PaginationParams): Promise<Bundle[]>`
- `getBundle(bundleId: string): Promise<Bundle>`

#### User Management
- `createUser(userData: CreateUserRequest): Promise<User>`
- `updateUser(userId: string, userData: UpdateUserRequest): Promise<User>`
- `updateUserTags(userId: string, tagData: UpdateUserTagsRequest): Promise<User>`
- `getUser(userId: string): Promise<User>`
- `getUserEnrollments(userId: string): Promise<Enrollment[]>`

#### Enrollment Management
- `enrollUserToProduct(enrollmentData: EnrollUserRequest): Promise<Enrollment>`
- `unenrollUserFromProduct(unenrollmentData: UnenrollUserRequest): Promise<void>`

### Token Management

```typescript
// Get current tokens
const tokens = client.auth.getTokens();

// Set tokens (session restoration)
client.auth.setTokens({
  accessToken: 'stored-access-token',
  refreshToken: 'stored-refresh-token',
  expiresAt: new Date('2024-12-31T00:00:00Z'),
});

// Automatic refresh with persistence
const client = new LearnWorldsClient({
  // ... config
  onTokenRefresh: async (tokens) => {
    await saveTokensToDatabase(tokens);
  },
});
```

## File Structure

```
learnworlds-js/
├── .github/workflows/     # GitHub Actions CI/CD
│   └── release.yml       # Automated release workflow
├── .serena/              # Serena AI configuration
├── src/                  # Source code
│   ├── client.ts         # Main client implementation
│   ├── oauth.ts          # OAuth2 authentication manager
│   ├── types.ts          # TypeScript type definitions
│   ├── index.ts          # Main exports
│   └── __tests__/        # Test files
├── dist/                 # Built output (ESM + CJS)
├── .releaserc.json      # Semantic release configuration
├── CHANGELOG.md         # Auto-generated changelog
├── package.json         # Package configuration
├── tsconfig.json        # TypeScript configuration
├── vitest.config.ts     # Test configuration
└── .eslintrc.json       # ESLint configuration
```

### Important Configuration Files

- **.releaserc.json**: Semantic release configuration for automated versioning
- **package.json**: Dual format exports (ESM/CJS), comprehensive metadata
- **tsconfig.json**: Strict TypeScript configuration
- **.github/workflows/release.yml**: Automated CI/CD pipeline

## Recent Updates (Updated: 2025-08-06)

### Major Changes in v1.0.0 Release
- **Package Rename**: Changed from `learnworlds-sdk` to `learnworlds-js`
- **Repository Migration**: Updated to `git@github.com:checkoutjoy/learnworlds-js.git`
- **Automated Release Pipeline**: Added semantic-release with GitHub Actions
- **Enhanced Package Configuration**: 
  - Dual format support (ESM + CommonJS)
  - Comprehensive package metadata
  - Node.js >= 16.0.0 requirement
  - Public NPM publishing configuration

### New Features
- **Semantic Release**: Automated versioning and changelog generation
- **GitHub Actions**: Complete CI/CD pipeline with automated releases
- **Enhanced Build System**: Modern tsup-based build with dual format output
- **Improved Package Structure**: Better export maps and file organization

### Configuration Updates
- Added `.releaserc.json` for semantic release configuration
- Enhanced `package.json` with comprehensive metadata and dual exports
- Added `CHANGELOG.md` with automated generation
- Configured GitHub Actions workflow for automated releases

### Development Workflow Improvements
- Added `prepublishOnly` script for automated pre-publish builds
- Enhanced package files configuration
- Added comprehensive keywords for better discoverability
- Improved author and repository metadata

## Important Notes

### Dual Host Configuration
LearnWorlds uses separate hosts for OAuth and API operations:
- **OAuth Host**: `https://yourschool.learnworlds.com` (for authentication)  
- **API Host**: `https://your-unique-api-host/v2` (for API calls)

You must configure both `schoolDomain` and `apiHost` in your client configuration.

### API Version
This SDK targets LearnWorlds API v2. Version 1 is no longer supported.

### Error Handling
All API errors throw `LearnWorldsError` instances with detailed information including status codes, error codes, messages, and additional details.

### Type Safety
The SDK provides complete TypeScript definitions for all API methods, request/response objects, and error types, ensuring full compile-time type checking.

### Token Persistence
The SDK supports automatic token refresh with custom persistence callbacks, allowing you to store updated tokens in your preferred storage system (database, cache, etc.).