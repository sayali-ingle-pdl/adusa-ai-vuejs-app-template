# Figma API Reference

## Authentication
```typescript
const FIGMA_API_BASE = 'https://api.figma.com/v1'
const headers = {
  'X-Figma-Token': accessToken,
  'Content-Type': 'application/json'
}
```

## Core API Endpoints

### Get File
```typescript
GET /files/{key}
// Returns file metadata, document structure, and components
```

### Get File Nodes
```typescript
GET /files/{key}/nodes?ids={nodeIds}
// Returns specific nodes within a file
```

### Get Images
```typescript
GET /images/{key}?ids={nodeIds}&format={format}
// Returns download URLs for node images
```

### Get Comments
```typescript
GET /files/{key}/comments
// Returns comments on the file
```

## Data Structures

### File Response
```typescript
interface FigmaFile {
  document: DocumentNode
  components: Record<string, Component>
  styles: Record<string, Style>
  schemaVersion: number
  name: string
  thumbnailUrl: string
  version: string
}
```

### Node Structure
```typescript
interface Node {
  id: string
  name: string
  type: NodeType
  visible?: boolean
  children?: Node[]
  absoluteBoundingBox?: Rectangle
  constraints?: LayoutConstraint
  fills?: Paint[]
  strokes?: Paint[]
  effects?: Effect[]
}
```

### Component Definition
```typescript
interface Component {
  key: string
  name: string
  description: string
  componentSetId?: string
  documentationLinks: DocumentationLink[]
}
```

### Style Definition
```typescript
interface Style {
  key: string
  name: string
  styleType: StyleType
  description: string
}

type StyleType = 'FILL' | 'TEXT' | 'EFFECT' | 'GRID'
```

## Rate Limiting
- 1000 requests per hour per access token
- Implements exponential backoff on rate limit hits
- Batches requests when possible

## Error Codes
- `400` - Bad Request (invalid parameters)
- `403` - Forbidden (invalid token or no access)
- `404` - Not Found (file doesn't exist)
- `429` - Too Many Requests (rate limited)
- `500` - Internal Server Error

## Best Practices
1. Cache responses when possible
2. Use batch requests for multiple nodes
3. Implement proper error handling and retries
4. Validate tokens before making requests
5. Monitor rate limit headers