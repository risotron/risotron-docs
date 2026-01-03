# URI & Buffer

Data types for resource identification and binary data.

---

## URI

Universal Resource Identifier for cross-platform path handling.

### Creating URIs

```typescript
import { URI } from 'risotron';

// From file path
const fileUri = URI.file('/path/to/file.ts');

// Parse string
const httpUri = URI.parse('https://example.com/api');

// From components
const customUri = URI.from({
  scheme: 'custom',
  authority: 'host',
  path: '/path',
  query: 'key=value',
  fragment: 'section'
});
```

### URI Properties

```typescript
const uri = URI.file('/path/to/file.ts');

uri.scheme     // 'file'
uri.authority  // ''
uri.path       // '/path/to/file.ts'
uri.query      // ''
uri.fragment   // ''
uri.fsPath     // '/path/to/file.ts' (OS-specific)
```

### Modifying URIs

```typescript
// URIs are immutable - with() returns new URI
const newUri = uri.with({
  path: '/new/path.ts'
});
```

### URI Utilities

```typescript
import {
  URI,
  uriDirname,
  uriBasename,
  uriExtname,
  uriIsEqual
} from 'risotron';

const uri = URI.file('/path/to/file.ts');

uriDirname(uri)           // URI: /path/to
uriBasename(uri)          // 'file.ts'
uriExtname(uri)           // '.ts'

// Join paths
const child = URI.joinPath(uri, '..', 'other.ts');
// URI: /path/to/other.ts

// Compare
uriIsEqual(uri1, uri2)    // boolean
```

### Schemes

```typescript
import { Schemes } from 'risotron';

Schemes.file          // 'file'
Schemes.http          // 'http'
Schemes.https         // 'https'
Schemes.untitled      // 'untitled'
Schemes.vscode        // 'vscode'
```

---

## VSBuffer

Binary data wrapper for cross-platform compatibility.

### Creating Buffers

```typescript
import { VSBuffer } from 'risotron';

// From string
const buf = VSBuffer.fromString('Hello, World!');

// From base64
const decoded = VSBuffer.fromBase64('SGVsbG8=');

// Allocate empty
const empty = VSBuffer.alloc(1024);

// Wrap existing Uint8Array
const wrapped = VSBuffer.wrap(uint8Array);
```

### Buffer Properties

```typescript
buf.buffer      // Underlying Uint8Array
buf.byteLength  // Number of bytes
```

### Converting

```typescript
// To string (UTF-8)
const str = buf.toString();

// To base64
const b64 = buf.toBase64();
```

### Operations

```typescript
// Slice
const part = buf.slice(0, 5);

// Clone
const copy = buf.clone();

// Concatenate
const combined = VSBuffer.concat([buf1, buf2, buf3]);

// Set data
buf.set(otherBuf, offset);
```

### Binary Read/Write

```typescript
const data = VSBuffer.alloc(8);

// Write
data.writeUInt8(0xFF, 0);
data.writeUInt32BE(0x12345678, 4);

// Read
const byte = data.readUInt8(0);
const int = data.readUInt32BE(4);
```

### With File Service

```typescript
const fileService = app.getService(IFileService);

// Write
await fileService.writeFile(
  URI.file('/path/to/file.bin'),
  VSBuffer.fromString('content')
);

// Read
const content = await fileService.readFile(URI.file('/path/to/file.bin'));
const text = content.value.toString();
```
