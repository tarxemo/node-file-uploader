# @tarxemo/file-uploader

A modular Node.js file upload library designed for flexibility across different projects and storage backends.

## Features

- **Multi-Storage Support**: Local Disk, AWS S3, and Minio (S3-compatible).
- **Automatic Image Optimization**: Auto-resize, rotate, and compress images (JPEG/WebP) using `sharp`.
- **ZIP Handling**: Automatically extracts ZIP files and uploads individual contents (preserving structure).
- **Stream-Aware**: Optimized for handling large files.
- **Pluggable Architecture**: Easily add your own storage providers or file processors.

## Installation

```bash
npm install @tarxemo/node-uploader
```

## Quick Start

The easiest way to get started is using the `createFromEnv` factory, which configures the library based on environment variables.

```javascript
const { createFromEnv } = require('@tarxemo/node-uploader');

// Initializes storage based on STORAGE_DRIVER (s3, minio, or disk)
const uploader = createFromEnv(process.env);

async function handleUpload(sessionId, files) {
  // 1. Initialize session (creates temp directory)
  const id = await uploader.initSession({ 
    username: 'user123', 
    uploadPath: 'photos/vacation' 
  });

  // 2. Add files (typically from multer or similar)
  for (const file of files) {
    await uploader.addFile(id, file);
  }

  // 3. Complete and process (compresses images, extracts zips, moves to final storage)
  const results = await uploader.completeSession(id);
  
  console.log(results);
}
```

## Component Usage

You can also instantiate components manually for full control.

### Storage Providers

#### Local Disk
```javascript
const { DiskProvider } = require('@tarxemo/node-uploader');

const storage = new DiskProvider({
  mediaRoot: './media',
  baseUrl: 'http://localhost:3000/media'
});
```

#### AWS S3 / Minio
```javascript
const { S3Provider } = require('@tarxemo/node-uploader');

const storage = new S3Provider({
  bucket: 'my-bucket',
  region: 'us-east-1',
  accessKeyId: '...',
  secretAccessKey: '...',
  // For Minio:
  // endpoint: 'http://localhost:9000',
  // forcePathStyle: true
});
```

### Image Processor
```javascript
const { ImageProcessor } = require('@tarxemo/node-uploader');

const processor = new ImageProcessor({
  maxWidth: 2000,
  targetMaxBytes: 2 * 1024 * 1024 // 2MB
});
```

### Upload Manager
```javascript
const { UploadManager } = require('@tarxemo/node-uploader');

const uploader = new UploadManager({
  storage,
  processor,
  tempDir: './uploads/temp'
});
```

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `STORAGE_DRIVER` | `disk`, `s3`, or `minio` | `disk` |
| `MEDIA_ROOT` | (Disk) Local storage path | `./media` |
| `AWS_S3_BUCKET` | (S3/Minio) Bucket name | - |
| `IMAGE_MAX_WIDTH` | Max width for images | `2000` |
| `IMAGE_TARGET_MAX_BYTES` | Target size for compression | `2MB` |

## License

ISC
# node-file-uploader
