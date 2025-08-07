# TypeScript Style Guide para SportProyect

## Configuración TypeScript

### tsconfig.json Principal

```json
{
  "compileOnSave": false,
  "compilerOptions": {
    "outDir": "./dist/out-tsc",
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "sourceMap": true,
    "declaration": false,
    "experimentalDecorators": true,
    "moduleResolution": "node",
    "importHelpers": true,
    "target": "ES2022",
    "module": "ES2022",
    "useDefineForClassFields": false,
    "lib": [
      "ES2022",
      "dom"
    ],
    "paths": {
      "@app/*": ["src/app/*"],
      "@core/*": ["src/app/core/*"],
      "@shared/*": ["src/app/shared/*"],
      "@features/*": ["src/app/features/*"],
      "@env/*": ["src/environments/*"],
      "@assets/*": ["src/assets/*"]
    }
  },
  "angularCompilerOptions": {
    "enableI18nLegacyMessageIdFormat": false,
    "strictInjectionParameters": true,
    "strictInputAccessModifiers": true,
    "strictTemplates": true,
    "strictNullChecks": true
  }
}
```

## Tipos y Interfaces

### Definición de Modelos

```typescript
// Prefer interfaces for object shapes
export interface User {
  id: string;
  email: string;
  username: string;
  firstName: string;
  lastName: string;
  role: UserRole;
  createdAt: Date;
  updatedAt: Date;
  lastLoginAt?: Date;
  preferences?: UserPreferences;
}

// Use type aliases for unions, intersections, and utility types
export type UserRole = 'admin' | 'coach' | 'player' | 'viewer';

export type UserStatus = 'active' | 'inactive' | 'suspended';

// Utility types
export type Nullable<T> = T | null;
export type Optional<T> = T | undefined;
export type AsyncData<T> = {
  data: T | null;
  loading: boolean;
  error: Error | null;
};

// Branded types for type safety
export type UserId = string & { readonly brand: unique symbol };
export type TeamId = string & { readonly brand: unique symbol };

// Factory functions for branded types
export const UserId = (id: string): UserId => id as UserId;
export const TeamId = (id: string): TeamId => id as TeamId;
```

### DTOs y Request/Response Types

```typescript
// Request DTOs
export interface CreateUserRequest {
  email: string;
  password: string;
  username: string;
  firstName: string;
  lastName: string;
}

export interface UpdateUserRequest {
  username?: string;
  firstName?: string;
  lastName?: string;
  preferences?: Partial<UserPreferences>;
}

// Response DTOs
export interface ApiResponse<T> {
  data: T;
  message?: string;
  timestamp: string;
}

export interface PaginatedResponse<T> {
  items: T[];
  total: number;
  page: number;
  pageSize: number;
  hasNext: boolean;
  hasPrevious: boolean;
}

export interface ErrorResponse {
  error: {
    code: string;
    message: string;
    details?: Record<string, string[]>;
  };
  timestamp: string;
  path: string;
}
```

## Enums y Constants

```typescript
// Prefer const assertions over enums when possible
export const MatchStatus = {
  SCHEDULED: 'scheduled',
  IN_PROGRESS: 'in_progress',
  COMPLETED: 'completed',
  CANCELLED: 'cancelled',
  POSTPONED: 'postponed'
} as const;

export type MatchStatus = typeof MatchStatus[keyof typeof MatchStatus];

// Use enums only when needed for reverse mapping
export enum HttpStatus {
  OK = 200,
  CREATED = 201,
  BAD_REQUEST = 400,
  UNAUTHORIZED = 401,
  FORBIDDEN = 403,
  NOT_FOUND = 404,
  INTERNAL_SERVER_ERROR = 500
}

// Constants with type safety
export const APP_CONFIG = {
  API_TIMEOUT: 30000,
  MAX_RETRY_ATTEMPTS: 3,
  DEBOUNCE_TIME: 300,
  PAGE_SIZE: 20,
  CACHE_DURATION: 5 * 60 * 1000, // 5 minutes
} as const;

// Type-safe feature flags
export const FEATURES = {
  REAL_TIME_UPDATES: true,
  DARK_MODE: true,
  EXPORT_DATA: false,
  BETA_FEATURES: false
} as const satisfies Record<string, boolean>;
```

## Generics

```typescript
// Generic interfaces
export interface Repository<T, ID = string> {
  findById(id: ID): Promise<T | null>;
  findAll(): Promise<T[]>;
  create(entity: Omit<T, 'id'>): Promise<T>;
  update(id: ID, entity: Partial<T>): Promise<T>;
  delete(id: ID): Promise<void>;
}

// Generic functions
export function groupBy<T, K extends keyof T>(
  items: T[],
  key: K
): Map<T[K], T[]> {
  return items.reduce((map, item) => {
    const groupKey = item[key];
    const group = map.get(groupKey) || [];
    group.push(item);
    map.set(groupKey, group);
    return map;
  }, new Map<T[K], T[]>());
}

// Generic constraints
export function sortBy<T extends Record<string, any>>(
  items: T[],
  key: keyof T,
  order: 'asc' | 'desc' = 'asc'
): T[] {
  return [...items].sort((a, b) => {
    const aVal = a[key];
    const bVal = b[key];
    
    if (aVal < bVal) return order === 'asc' ? -1 : 1;
    if (aVal > bVal) return order === 'asc' ? 1 : -1;
    return 0;
  });
}

// Conditional types
export type DeepPartial<T> = T extends object
  ? { [P in keyof T]?: DeepPartial<T[P]> }
  : T;

export type DeepReadonly<T> = T extends object
  ? { readonly [P in keyof T]: DeepReadonly<T[P]> }
  : T;
```

## Type Guards

```typescript
// User-defined type guards
export function isUser(obj: any): obj is User {
  return (
    typeof obj === 'object' &&
    obj !== null &&
    'id' in obj &&
    'email' in obj &&
    'username' in obj
  );
}

export function isError(value: unknown): value is Error {
  return value instanceof Error;
}

export function isApiError(error: unknown): error is ApiError {
  return (
    isError(error) &&
    'status' in error &&
    'code' in error
  );
}

// Discriminated unions
export type Result<T, E = Error> =
  | { success: true; data: T }
  | { success: false; error: E };

export function isSuccess<T, E>(result: Result<T, E>): result is { success: true; data: T } {
  return result.success === true;
}

// Type predicates with generics
export function isDefined<T>(value: T | undefined | null): value is T {
  return value !== undefined && value !== null;
}

export function isNonEmptyArray<T>(value: T[]): value is [T, ...T[]] {
  return value.length > 0;
}
```

## Utility Types Personalizados

```typescript
// Extract keys of specific type
export type KeysOfType<T, U> = {
  [K in keyof T]: T[K] extends U ? K : never;
}[keyof T];

// Example usage
type StringKeys = KeysOfType<User, string>; // 'id' | 'email' | 'username' | ...

// Make specific properties required
export type RequireFields<T, K extends keyof T> = T & Required<Pick<T, K>>;

// Make specific properties optional
export type PartialFields<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;

// Mutable type (remove readonly)
export type Mutable<T> = {
  -readonly [P in keyof T]: T[P];
};

// DeepMutable
export type DeepMutable<T> = {
  -readonly [P in keyof T]: DeepMutable<T[P]>;
};

// Function types
export type AsyncFunction<T = void> = () => Promise<T>;
export type Callback<T = void> = (error: Error | null, result?: T) => void;
export type EventHandler<T = Event> = (event: T) => void;
```

## Mapped Types y Template Literal Types

```typescript
// API endpoints type safety
export type ApiEndpoints = {
  users: {
    list: '/api/users';
    detail: '/api/users/:id';
    create: '/api/users';
    update: '/api/users/:id';
    delete: '/api/users/:id';
  };
  teams: {
    list: '/api/teams';
    detail: '/api/teams/:id';
    players: '/api/teams/:id/players';
  };
};

// Type-safe route params
export type ExtractRouteParams<T extends string> = 
  T extends `${string}:${infer Param}/${infer Rest}`
    ? Param | ExtractRouteParams<`/${Rest}`>
    : T extends `${string}:${infer Param}`
    ? Param
    : never;

// Usage
type UserDetailParams = ExtractRouteParams<ApiEndpoints['users']['detail']>; // 'id'

// Event types with template literals
export type DomainEvents = {
  user: 'created' | 'updated' | 'deleted';
  team: 'created' | 'updated' | 'deleted';
  match: 'scheduled' | 'started' | 'completed' | 'cancelled';
};

export type EventName = {
  [K in keyof DomainEvents]: `${K}:${DomainEvents[K]}`;
}[keyof DomainEvents];
// Results in: 'user:created' | 'user:updated' | ... | 'match:cancelled'
```

## Decorators (cuando sean necesarios)

```typescript
// Method decorator for logging
export function Log(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;
  
  descriptor.value = async function (...args: any[]) {
    console.log(`Calling ${propertyKey} with args:`, args);
    
    try {
      const result = await originalMethod.apply(this, args);
      console.log(`${propertyKey} returned:`, result);
      return result;
    } catch (error) {
      console.error(`${propertyKey} threw error:`, error);
      throw error;
    }
  };
  
  return descriptor;
}

// Property decorator for validation
export function MinLength(length: number) {
  return function (target: any, propertyKey: string) {
    let value: string;
    
    const getter = () => value;
    const setter = (newValue: string) => {
      if (newValue.length < length) {
        throw new Error(`${propertyKey} must be at least ${length} characters`);
      }
      value = newValue;
    };
    
    Object.defineProperty(target, propertyKey, {
      get: getter,
      set: setter,
      enumerable: true,
      configurable: true
    });
  };
}
```

## Módulos y Namespaces

```typescript
// Module augmentation
declare module '@angular/core' {
  interface Signal<T> {
    pipe<R>(fn: (value: T) => R): Signal<R>;
  }
}

// Global type augmentation
declare global {
  interface Window {
    __APP_VERSION__: string;
    __FEATURE_FLAGS__: Record<string, boolean>;
  }
  
  interface Array<T> {
    groupBy<K extends string | number | symbol>(
      keyFn: (item: T) => K
    ): Record<K, T[]>;
  }
}

// Namespace for organizing types
export namespace SportModels {
  export interface Team {
    id: TeamId;
    name: string;
    city: string;
    founded: number;
    stadium: Stadium;
    players: Player[];
  }
  
  export interface Player {
    id: string;
    name: string;
    position: Position;
    number: number;
    teamId: TeamId;
  }
  
  export interface Stadium {
    name: string;
    capacity: number;
    location: Coordinates;
  }
  
  export type Position = 'goalkeeper' | 'defender' | 'midfielder' | 'forward';
  
  export interface Coordinates {
    latitude: number;
    longitude: number;
  }
}
```

## Type Assertions y Type Narrowing

```typescript
// Safe type assertions
export function assertDefined<T>(
  value: T | null | undefined,
  message?: string
): asserts value is T {
  if (value === null || value === undefined) {
    throw new Error(message || 'Value is null or undefined');
  }
}

export function assertType<T>(
  value: unknown,
  checker: (value: unknown) => value is T,
  message?: string
): asserts value is T {
  if (!checker(value)) {
    throw new Error(message || 'Type assertion failed');
  }
}

// Usage
function processUser(data: unknown) {
  assertType(data, isUser, 'Invalid user data');
  // Now TypeScript knows data is User
  console.log(data.email);
}

// Exhaustive checks
export function assertNever(value: never): never {
  throw new Error(`Unexpected value: ${value}`);
}

// Usage in switch statements
function handleMatchStatus(status: MatchStatus): string {
  switch (status) {
    case 'scheduled':
      return 'Match is scheduled';
    case 'in_progress':
      return 'Match is in progress';
    case 'completed':
      return 'Match completed';
    case 'cancelled':
      return 'Match cancelled';
    case 'postponed':
      return 'Match postponed';
    default:
      return assertNever(status); // Ensures all cases are handled
  }
}
```

## Async/Promise Types

```typescript
// Promise utility types
export type PromiseType<T extends Promise<any>> = T extends Promise<infer U> ? U : never;

// Async function return type
export type AsyncReturnType<T extends (...args: any[]) => Promise<any>> =
  T extends (...args: any[]) => Promise<infer R> ? R : never;

// Error handling types
export type SafePromise<T> = Promise<Result<T>>;

export async function safeAsync<T>(
  fn: () => Promise<T>
): SafePromise<T> {
  try {
    const data = await fn();
    return { success: true, data };
  } catch (error) {
    return { success: false, error: error as Error };
  }
}

// Retry logic with types
export interface RetryOptions {
  maxAttempts: number;
  delay: number;
  backoff?: 'linear' | 'exponential';
}

export async function withRetry<T>(
  fn: () => Promise<T>,
  options: RetryOptions
): Promise<T> {
  let lastError: Error;
  
  for (let attempt = 1; attempt <= options.maxAttempts; attempt++) {
    try {
      return await fn();
    } catch (error) {
      lastError = error as Error;
      
      if (attempt < options.maxAttempts) {
        const delay = options.backoff === 'exponential'
          ? options.delay * Math.pow(2, attempt - 1)
          : options.delay;
          
        await new Promise(resolve => setTimeout(resolve, delay));
      }
    }
  }
  
  throw lastError!;
}
```

## Best Practices Summary

1. **Enable all strict flags** in tsconfig.json
2. **Prefer interfaces** over type aliases for object shapes
3. **Use const assertions** for literal types
4. **Avoid any** - use unknown for truly unknown types
5. **Use type guards** for runtime type checking
6. **Leverage utility types** (Partial, Required, Pick, Omit, etc.)
7. **Use discriminated unions** for state management
8. **Prefer readonly** for immutable data
9. **Use branded types** for domain primitives
10. **Document complex types** with JSDoc comments
11. **Use template literal types** for string manipulation
12. **Avoid type assertions** unless absolutely necessary
13. **Use strict null checks** always
14. **Prefer unknown over any** in catch blocks
15. **Use exhaustive checks** in switch statements