# CodeLockr JS Runtime

Enterprise-grade runtime for executing AES-256-GCM encrypted JavaScript, TypeScript, and Next.js server-side source files.

## Installation

```bash
npm install @codelockr/runtime
```

> The runtime is also bundled inside every `.clr.js` file, so no additional setup is required at runtime.

## How It Works

Encrypted `.clr.js` files are self-contained — they include the runtime and decrypt themselves in memory when loaded. All you need is a regular `.js` or `.ts` file that requires or imports the encrypted file.

## Usage

### Node.js / CommonJS

```js
// index.js
require('./modules/dashboard.clr.js');
```

### TypeScript / ESM

```ts
// index.ts
import './modules/dashboard.clr.js';
```

### Next.js — API Route

```ts
// app/api/dashboard/route.ts
import './modules/dashboard.clr.js';
```

> **Note:** Always load `.clr.js` files in a **server-side** context only — API routes, Server Components, or Node.js scripts. Never import them in Client Components.

> **Important:** Never place `.clr.js` files inside a `public/` or statically served directory.

## Deployment Checklist

1. Run `npm install` in your project directory
2. Place `.clr.js` files **outside** any public or statically served folder
3. Load them via a regular `.js` or `.ts` entry file
4. For Next.js: only load inside **API routes** or **Server Components**

## Security Features

- AES-256-GCM encryption
- HMAC-SHA256 integrity validation
- Fragmented key reconstruction
- Temporary decryption to memory (no disk writes)
- Self-contained — runtime is bundled inside the encrypted file

## License

MIT License