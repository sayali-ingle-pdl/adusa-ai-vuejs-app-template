# Global Constants Skill

## Purpose
Generate the `src/shared/constants/global.ts` file for application-wide constants.

## Input Parameters
- `application_id`: The application ID

## Output
Create the file: `src/shared/constants/global.ts`

## Template

```typescript
// Global application constants

export const appId = '{{application_id}}';
```

## Notes
- Contains constants used throughout the application
- The appId is used for CSS isolation via style tag IDs
- Can be extended with other global constants as needed
