# Prerequisites

[&larr; Back to Home](../README.md) | [Next: Getting Started &rarr;](01-getting-started.md)

---

Before diving into Angular, you need a working knowledge of TypeScript and a configured development environment. This page covers exactly what you need — nothing more.

## Table of Contents

- [TypeScript Essentials](#typescript-essentials)
- [Development Environment](#development-environment)
- [Key Takeaways](#key-takeaways)

---

## TypeScript Essentials

Angular is written in TypeScript. You don't need to be a TypeScript expert, but you need to be comfortable with these features.

### Types and Interfaces

```typescript
// Basic types
let name: string = 'Angular';
let version: number = 19;
let isModern: boolean = true;
let items: string[] = ['signals', 'standalone', 'ssr'];

// Interfaces define the shape of objects
interface User {
  id: number;
  name: string;
  email: string;
  role?: string;  // optional property
}

const user: User = { id: 1, name: 'Ada', email: 'ada@example.com' };
```

### Functions with Types

```typescript
// Typed parameters and return values
function greet(name: string): string {
  return `Hello, ${name}!`;
}

// Arrow functions
const double = (n: number): number => n * 2;

// Functions that return nothing
function log(message: string): void {
  console.log(message);
}
```

### Generics

Generics let you write reusable code that works with multiple types. You'll encounter them constantly in Angular (e.g., `Signal<string>`, `Observable<User[]>`).

```typescript
// A function that works with any type
function first<T>(items: T[]): T | undefined {
  return items[0];
}

first<string>(['a', 'b']);  // returns 'a' (string)
first<number>([1, 2, 3]);   // returns 1 (number)

// Generic interface
interface ApiResponse<T> {
  data: T;
  status: number;
}

const response: ApiResponse<User[]> = {
  data: [{ id: 1, name: 'Ada', email: 'ada@example.com' }],
  status: 200
};
```

### Classes and Decorators

Angular components are classes with decorators. Decorators are functions that modify class behavior.

```typescript
// A simple class
class Animal {
  constructor(public name: string, private sound: string) {}

  speak(): string {
    return `${this.name} says ${this.sound}`;
  }
}

const dog = new Animal('Rex', 'woof');
dog.speak(); // 'Rex says woof'
```

```typescript
// Decorators (you'll see these everywhere in Angular)
// Angular provides decorators like @Component, @Injectable, etc.
// You don't write your own — just understand the syntax:

@Component({
  selector: 'app-hello',
  template: '<h1>Hello</h1>'
})
class HelloComponent {}
```

> **Note:** `public` in the constructor parameter automatically creates and assigns a property. `private` does the same but restricts access to within the class.

### Enums and Union Types

```typescript
// Enums
enum Status {
  Active = 'ACTIVE',
  Inactive = 'INACTIVE',
  Pending = 'PENDING'
}

// Union types (often preferred over enums)
type Theme = 'light' | 'dark' | 'system';

let currentTheme: Theme = 'dark';
```

### Utility Types

```typescript
// Partial — makes all properties optional
interface Config {
  apiUrl: string;
  timeout: number;
  debug: boolean;
}
function updateConfig(partial: Partial<Config>) { /* ... */ }
updateConfig({ debug: true }); // only override what you need

// Pick — select specific properties
type Credentials = Pick<User, 'email' | 'name'>;

// Omit — exclude specific properties
type PublicUser = Omit<User, 'email'>;

// Record — typed key-value maps
const errors: Record<string, string> = {
  required: 'This field is required',
  email: 'Invalid email format'
};
```

### ES6+ Features Used in Angular

```typescript
// Destructuring
const { name, email } = user;
const [first, ...rest] = items;

// Spread operator
const updated = { ...user, name: 'Updated' };
const combined = [...items, 'new-item'];

// Template literals
const greeting = `Hello, ${name}!`;

// Optional chaining and nullish coalescing
const role = user.role ?? 'guest';
const city = user?.address?.city;

// async/await
async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}
```

---

## Development Environment

### Required Software

| Tool | Purpose | Install |
|------|---------|---------|
| **Node.js** (v18.19+) | JavaScript runtime | [nodejs.org](https://nodejs.org) |
| **npm** (comes with Node) | Package manager | Included with Node.js |
| **Angular CLI** | Project scaffolding and tooling | `npm install -g @angular/cli` |
| **VS Code** | Recommended editor | [code.visualstudio.com](https://code.visualstudio.com) |

### Verify Installation

```bash
node --version    # Should be v18.19 or later
npm --version     # Should be v9 or later
ng version        # Should show Angular CLI version
```

### Recommended VS Code Extensions

| Extension | Purpose |
|-----------|---------|
| **Angular Language Service** | Autocomplete, error checking in templates |
| **Prettier** | Code formatting |
| **ESLint** | Linting |
| **EditorConfig** | Consistent editor settings |

### Useful Angular CLI Commands

```bash
ng new my-app           # Create a new project
ng serve                # Start dev server (http://localhost:4200)
ng generate component   # Generate a component
ng build                # Build for production
ng test                 # Run unit tests
ng lint                 # Run linter
```

> **Tip:** Use `ng g c` as shorthand for `ng generate component`. See the [Getting Started](01-getting-started.md) guide for a full walkthrough.

---

## Key Takeaways

- You need **TypeScript fundamentals**: types, interfaces, generics, classes, decorators
- ES6+ features (destructuring, spread, async/await, template literals) are used everywhere
- Install **Node.js**, the **Angular CLI**, and **VS Code** with the Angular Language Service
- You do **not** need to master TypeScript before starting — learn as you go

---

## Free Resources

> **YouTube:** [TypeScript Tutorial for Angular Developers](https://www.youtube.com/@DecodedFrontend) — Decoded Frontend covers exactly the TypeScript features you need for Angular (decorators, generics, utility types)
>
> **YouTube:** [TypeScript in 100 Seconds](https://www.youtube.com/@Fireship) — Fireship's fast orientation, then go deeper with the handbook below
>
> **Official:** [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/) — the definitive free TypeScript reference
>
> **Interactive:** [TypeScript Exercises](https://typescript-exercises.github.io/) — browser-based exercises that build progressively, great before starting Angular

---

[&larr; Back to Home](../README.md) | [Next: Getting Started &rarr;](01-getting-started.md)
