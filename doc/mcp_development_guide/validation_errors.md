# Validation & Error Handling

## Table of Contents
- [Input Validation Utilities](#input-validation-utilities)
- [Validation Error Types](#validation-error-types)
- [Error Mapping](#error-mapping)

## Input Validation Utilities
Validation helpers ensure required fields and data types are correct before tools run.

```ts
static validateString(value: any, fieldName: string, required: boolean = true): ValidationResult {
  if (required && (value === undefined || value === null)) {
    errors.push({ field: fieldName, message: `${fieldName} is required`, value });
  }
}
```

## Validation Error Types
The MCP error hierarchy standardizes error codes and status codes.

```ts
export class ValidationError extends MCPError {
  constructor(message: string, data?: any) {
    super(message, 'VALIDATION_ERROR', 400, data);
  }
}
```

## Error Mapping
Errors from HTTP clients are mapped into MCP error types for consistent responses.

```ts
if (error.response) {
  const status = error.response.status;
  const message = error.response.data?.message || error.message;

  if (status === 401) {
    return new AuthenticationError(message);
  }

  return new MCPError(message, 'API_ERROR', status);
}
```
