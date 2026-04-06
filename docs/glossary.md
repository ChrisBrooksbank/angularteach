# Glossary

[&larr; Back to Home](../README.md)

---

Quick definitions of Angular terms, linked to their detailed guides.

## A

**AOT (Ahead-of-Time) Compilation** — Compiling Angular templates at build time rather than in the browser. Default for production builds. See [Deployment](19-deployment.md).

**Attribute Directive** — A directive that changes the appearance or behavior of an element (e.g., `ngClass`, `ngStyle`). See [Directives & Pipes](06-directives-and-pipes.md).

## B

**Binding** — The mechanism connecting component data to the template. Includes interpolation (`{{ }}`), property (`[ ]`), event (`( )`), and two-way (`[( )]`). See [Templates & Data Binding](03-templates-and-binding.md).

**Bootstrap** — The process of starting an Angular application. Uses `bootstrapApplication()` in modern Angular. See [Getting Started](01-getting-started.md).

## C

**Change Detection** — Angular's mechanism for synchronizing component data with the DOM. Can be `Default` or `OnPush`. See [Change Detection](13-change-detection.md).

**Component** — The fundamental building block of Angular UI. A TypeScript class with an HTML template, CSS styles, and `@Component` metadata. See [Components](02-components.md).

**Computed Signal** — A read-only signal derived from other signals: `computed(() => a() + b())`. See [Signals](05-signals.md#computed-signals).

**Content Projection** — Inserting content from a parent into a child component using `<ng-content>`. Similar to "slots" in other frameworks. See [Components](02-components.md#content-projection).

**Control Flow** — Angular's template syntax for conditional rendering (`@if`), loops (`@for`), and pattern matching (`@switch`). See [Control Flow](04-control-flow.md).

## D

**Decorator** — A TypeScript feature (e.g., `@Component`, `@Injectable`) that attaches metadata to a class. See [Prerequisites](00-prerequisites.md).

**Dependency Injection (DI)** — A design pattern where classes receive their dependencies from an external source. Angular's DI system creates and delivers service instances. See [Services & DI](07-services-and-di.md).

**Directive** — A class that adds behavior to DOM elements. Three types: component directives, attribute directives, structural directives. See [Directives & Pipes](06-directives-and-pipes.md).

## E

**Effect** — A function that runs side effects when signals it reads change: `effect(() => ...)`. See [Signals](05-signals.md#effects).

## F

**FormControl** — The smallest unit of a reactive form, tracking the value and validation status of an individual input. See [Forms](09-forms.md).

**FormGroup** — A collection of `FormControl` instances that tracks their aggregate value and validation status. See [Forms](09-forms.md).

## G

**Guard** — A function that controls access to a route (e.g., authentication checks). See [Routing](08-routing.md#route-guards).

## H

**HttpClient** — Angular's service for making HTTP requests. Returns Observables. See [HTTP Client](10-http-client.md).

**httpResource()** — Signal-based HTTP fetching that auto-refetches when dependencies change. See [HTTP Client](10-http-client.md#httpresource).

**Hydration** — The process of making server-rendered HTML interactive by attaching event listeners without re-creating DOM elements. See [SSR & Hydration](15-ssr-and-hydration.md).

## I

**Inject()** — The modern function for requesting dependencies: `inject(MyService)`. Preferred over constructor injection. See [Services & DI](07-services-and-di.md#the-inject-function).

**Injection Token** — An object used as a key for non-class dependencies in the DI system. See [Services & DI](07-services-and-di.md#injection-tokens).

**Interceptor** — A function that modifies HTTP requests or responses globally (e.g., adding auth tokens). See [HTTP Client](10-http-client.md#interceptors).

**Interpolation** — Displaying component data in templates using `{{ expression }}`. See [Templates & Data Binding](03-templates-and-binding.md#interpolation).

## L

**Lazy Loading** — Loading parts of the application on demand instead of upfront. Applied to routes (`loadComponent`, `loadChildren`) and templates (`@defer`). See [Routing](08-routing.md#lazy-loading) and [Performance](16-performance.md).

**Lifecycle Hooks** — Methods Angular calls at specific moments in a component's life (e.g., `ngOnInit`, `ngOnDestroy`). See [Components](02-components.md#lifecycle-hooks).

**LinkedSignal** — A writable signal that resets when a source signal changes: `linkedSignal(() => source())`. See [Signals](05-signals.md#advanced-signals).

## M

**Model** — A signal-based API for two-way binding between parent and child: `model<T>()`. See [Components](02-components.md#two-way-binding-with-model).

## N

**NgModule** — The legacy way to organize Angular code into cohesive blocks. Replaced by standalone components in modern Angular. See [Advanced Patterns](18-advanced-patterns.md#ngmodule-migration).

## O

**Observable** — An RxJS data type representing a stream of values over time. Used by `HttpClient`, `Router`, and reactive forms. See [RxJS Essentials](11-rxjs.md).

**OnPush** — A change detection strategy that only checks a component when its inputs change or events fire within it. See [Change Detection](13-change-detection.md#default-vs-onpush).

## P

**Pipe** — A class that transforms data for display in templates: `{{ value | pipeName }}`. See [Directives & Pipes](06-directives-and-pipes.md#pipes-overview).

**Provider** — A configuration that tells Angular's DI system how to create a dependency. See [Services & DI](07-services-and-di.md).

## R

**Reactive Forms** — A form approach where the structure is defined in TypeScript using `FormGroup` and `FormControl`. See [Forms](09-forms.md#reactive-forms).

**Resolver** — A function that pre-fetches data before a route activates. See [Routing](08-routing.md#resolvers).

**Resource** — Signal-based async data loading: `resource({ request, loader })`. See [Signals](05-signals.md#advanced-signals).

**Router** — Angular's system for mapping URLs to components and managing navigation. See [Routing](08-routing.md).

**RouterOutlet** — A placeholder in the template where the router renders matched components: `<router-outlet />`. See [Routing](08-routing.md).

## S

**Selector** — The custom HTML tag for a component (e.g., `'app-my-component'` → `<app-my-component />`). See [Components](02-components.md).

**Service** — A class containing reusable business logic, decorated with `@Injectable`. See [Services & DI](07-services-and-di.md).

**Signal** — Angular's reactive primitive that holds a value and notifies consumers when it changes. See [Signals](05-signals.md).

**SSG (Static Site Generation)** — Generating HTML at build time for static pages. See [SSR & Hydration](15-ssr-and-hydration.md).

**SSR (Server-Side Rendering)** — Generating HTML on the server for each request. See [SSR & Hydration](15-ssr-and-hydration.md).

**Standalone Component** — A component that declares its own dependencies via `imports`, without needing an NgModule. Default since Angular 19. See [Components](02-components.md#standalone-components).

## T

**Template** — The HTML that defines a component's view. See [Templates & Data Binding](03-templates-and-binding.md).

**Template Reference Variable** — A variable declared in a template with `#name` for accessing elements: `<input #myInput />`. See [Templates & Data Binding](03-templates-and-binding.md#template-reference-variables).

**Track** — Required expression in `@for` loops that helps Angular identify items for efficient DOM updates. See [Control Flow](04-control-flow.md#the-track-expression-required).

## V

**ViewChild** — A signal-based query for accessing child elements/components: `viewChild('ref')`. See [Components](02-components.md#view-queries).

## Z

**Zone.js** — A library that patches browser APIs to trigger Angular's change detection automatically. Being replaced by zoneless Angular with signals. See [Change Detection](13-change-detection.md#zoneless-angular).

**Zoneless** — Running Angular without Zone.js, relying on signals for change detection. See [Change Detection](13-change-detection.md#zoneless-angular).

---

## Free Resources

> **Official:** [Angular Glossary](https://angular.dev/guide/glossary) — the official Angular glossary on angular.dev
>
> **Official:** [API Reference](https://angular.dev/api) — searchable API documentation for every Angular package

---

[&larr; Back to Home](../README.md)
