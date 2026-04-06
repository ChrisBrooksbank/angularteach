# Angular Cheatsheet

[&larr; Back to Home](../README.md)

---

Quick reference for modern Angular syntax and patterns.

## Component

```typescript
import { Component, signal, computed, input, output, model, inject } from '@angular/core';

@Component({
  selector: 'app-my-component',
  imports: [OtherComponent, SomePipe],
  templateUrl: './my.component.html',
  styleUrl: './my.component.css',
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class MyComponent {
  // Signals
  count = signal(0);
  doubled = computed(() => this.count() * 2);

  // Inputs
  name = input<string>('default');       // optional with default
  id = input.required<number>();          // required

  // Outputs
  clicked = output<void>();
  selected = output<string>();

  // Two-way binding
  value = model(0);

  // DI
  private router = inject(Router);
  private http = inject(HttpClient);

  // View queries
  myInput = viewChild<ElementRef>('myInput');
  cards = viewChildren(CardComponent);
}
```

## Template Binding

```html
{{ expression }}                           <!-- interpolation -->
[property]="expression"                    <!-- property binding -->
(event)="handler($event)"                 <!-- event binding -->
[(ngModel)]="property"                    <!-- two-way binding -->
[class.active]="isActive"                 <!-- class binding -->
[style.color]="color"                     <!-- style binding -->
[attr.aria-label]="label"                 <!-- attribute binding -->
#ref                                       <!-- template reference variable -->
```

## Control Flow

```html
@if (condition) {
  ...
} @else if (other) {
  ...
} @else {
  ...
}

@for (item of items; track item.id; let i = $index) {
  ...
} @empty {
  ...
}

@switch (value) {
  @case ('a') { ... }
  @case ('b') { ... }
  @default { ... }
}

@let name = expression;

@defer (on viewport) {
  <app-heavy />
} @placeholder { ... }
  @loading { ... }
  @error { ... }
```

## Signals

```typescript
// Create
const count = signal(0);

// Read
count()

// Write
count.set(5);
count.update(n => n + 1);

// Derive
const doubled = computed(() => count() * 2);

// Side effect
effect(() => console.log(count()));

// Advanced
const linked = linkedSignal(() => source());
const res = resource({ request: () => id(), loader: ... });
const sig = toSignal(observable$, { initialValue: 0 });
const obs$ = toObservable(signal);
```

## Services & DI

```typescript
@Injectable({ providedIn: 'root' })
export class MyService {
  private http = inject(HttpClient);
}

// In component
private myService = inject(MyService);

// Injection token
const API_URL = new InjectionToken<string>('API_URL');
{ provide: API_URL, useValue: 'https://api.example.com' }
```

## Routing

```typescript
// Routes
export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'user/:id', component: UserComponent },
  { path: 'admin', loadChildren: () => import('./admin/routes').then(m => m.routes) },
  { path: '**', redirectTo: '' }
];

// Config
provideRouter(routes, withComponentInputBinding(), withViewTransitions())

// Navigation
<a routerLink="/path" routerLinkActive="active">Link</a>
this.router.navigate(['/path', id], { queryParams: { key: 'value' } });

// Guards
export const authGuard: CanActivateFn = () => inject(AuthService).isLoggedIn();
```

## Forms

```typescript
// Reactive
form = inject(FormBuilder).group({
  name: ['', [Validators.required, Validators.minLength(2)]],
  email: ['', [Validators.required, Validators.email]]
});
```

```html
<form [formGroup]="form" (ngSubmit)="onSubmit()">
  <input formControlName="name" />
  <button [disabled]="form.invalid">Submit</button>
</form>
```

## HTTP

```typescript
// Setup: provideHttpClient(withInterceptors([authInterceptor]))

// GET
this.http.get<User[]>('/api/users')

// POST
this.http.post<User>('/api/users', body)

// httpResource
userResource = httpResource<User>(() => `/api/users/${this.id()}`);

// Interceptor
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = inject(AuthService).token();
  return next(token ? req.clone({ setHeaders: { Authorization: `Bearer ${token}` } }) : req);
};
```

## Pipes

```html
{{ date | date:'short' }}
{{ price | currency:'USD' }}
{{ name | uppercase }}
{{ items | slice:0:5 }}
{{ obj | json }}
{{ obs$ | async }}
```

## Lifecycle Hooks

```typescript
ngOnInit()          // after inputs set (initialization logic)
ngOnChanges()       // when input values change
ngAfterViewInit()   // after view rendered
ngOnDestroy()       // before component destroyed
```

## CLI Commands

```bash
ng new my-app                  # new project
ng serve                       # dev server
ng build                       # production build
ng test                        # run tests
ng g c my-component            # generate component
ng g s my-service              # generate service
ng g p my-pipe                 # generate pipe
ng g d my-directive            # generate directive
ng g guard my-guard            # generate guard
ng add @angular/ssr            # add SSR
ng update                      # update Angular
```

---

[&larr; Back to Home](../README.md)
