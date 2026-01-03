# Installation

## Requirements

- Node.js 18+
- pnpm (recommended) or npm

## Add to Existing Electron Forge Project

```bash
# Copy risotron source
cp -r risotron/ your-project/src/risotron/

# Update imports
# Before:
import { app, BrowserWindow } from 'electron';

# After:
import { createApplication, BrowserWindow } from './risotron';
```

## Development Commands

```bash
# Start with hot reload
pnpm start

# Build for production
pnpm make

# Run linting
pnpm lint
```

## TypeScript Configuration

Risotron requires these compiler options:

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "strict": true,
    "target": "ES2022",
    "module": "commonjs"
  }
}
```
