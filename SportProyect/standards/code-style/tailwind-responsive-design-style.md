# Tailwind CSS - Guía Completa para Diseño Responsivo en SportProyect

## NORMA OBLIGATORIA PARA ANGULAR

**⚠️ REGLA FUNDAMENTAL: TODOS los componentes Angular en SportProyect DEBEN usar exclusivamente TailwindCSS para el diseño responsivo. No se permite CSS personalizado para responsive design.**

Esta guía es la referencia oficial y debe seguirse estrictamente para mantener consistencia en todo el proyecto.

## Principios Fundamentales

### 1. Mobile-First Approach (OBLIGATORIO)
```html
<!-- ✅ CORRECTO: Mobile-first, luego breakpoints mayores -->
<div class="w-full md:w-1/2 lg:w-1/3">
  <!-- Contenido -->
</div>

<!-- ❌ INCORRECTO: No responsive -->
<div class="w-1/3">
  <!-- Contenido -->
</div>
```

### 2. Utility-First Classes (OBLIGATORIO)
```html
<!-- ✅ CORRECTO: Usando utilidades de Tailwind -->
<button class="bg-primary hover:bg-primary-600 px-4 py-2 rounded-md text-white font-medium transition-colors">
  Botón
</button>

<!-- ❌ INCORRECTO: CSS personalizado -->
<button class="custom-button">
  Botón
</button>
```

## Breakpoints Oficiales para SportProyect

```typescript
// Breakpoints que DEBES usar siempre
const breakpoints = {
  sm: '40rem (640px)',   // Tablets pequeñas
  md: '48rem (768px)',   // Tablets
  lg: '64rem (1024px)',  // Laptops
  xl: '80rem (1280px)',  // Desktops
  '2xl': '96rem (1536px)' // Pantallas grandes
};
```

## Patrones de Diseño Responsivo Obligatorios

### 1. Layout de Cards para Equipos
```typescript
@Component({
  template: `
    <!-- Grid responsivo obligatorio para cards -->
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4 sm:gap-6">
      <div *ngFor="let team of teams" class="card">
        <!-- Header de imagen -->
        <div class="aspect-w-16 aspect-h-9 sm:aspect-h-10 lg:aspect-h-9">
          <img 
            [src]="team.logo" 
            [alt]="team.name + ' logo'"
            class="w-full h-full object-cover"
          >
        </div>
        
        <!-- Contenido responsivo -->
        <div class="p-4 sm:p-6">
          <h3 class="text-lg sm:text-xl lg:text-2xl font-bold">
            {{ team.name }}
          </h3>
          <p class="text-sm sm:text-base text-muted-foreground mt-2">
            {{ team.city }}, {{ team.country }}
          </p>
          
          <!-- Stats grid responsivo -->
          <div class="grid grid-cols-3 gap-2 sm:gap-4 mt-4">
            <div class="text-center">
              <div class="text-xl sm:text-2xl lg:text-3xl font-bold text-primary">
                {{ team.wins }}
              </div>
              <div class="text-xs sm:text-sm text-muted-foreground">Wins</div>
            </div>
            <div class="text-center">
              <div class="text-xl sm:text-2xl lg:text-3xl font-bold text-muted-foreground">
                {{ team.draws }}
              </div>
              <div class="text-xs sm:text-sm text-muted-foreground">Draws</div>
            </div>
            <div class="text-center">
              <div class="text-xl sm:text-2xl lg:text-3xl font-bold text-destructive">
                {{ team.losses }}
              </div>
              <div class="text-xs sm:text-sm text-muted-foreground">Losses</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  `
})
export class TeamGridComponent {}
```

### 2. Layout Master (App Layout)
```typescript
@Component({
  template: `
    <!-- Layout principal responsivo -->
    <div class="min-h-screen bg-background">
      <!-- Header responsivo -->
      <header class="sticky top-0 z-50 border-b bg-background/95 backdrop-blur supports-[backdrop-filter]:bg-background/60">
        <div class="container mx-auto px-4 sm:px-6 lg:px-8">
          <div class="flex h-14 sm:h-16 lg:h-20 items-center justify-between">
            <!-- Logo responsivo -->
            <div class="flex items-center">
              <img 
                src="/assets/logo.svg" 
                alt="SportProyect"
                class="h-8 sm:h-10 lg:h-12 w-auto"
              >
              <h1 class="ml-3 text-xl sm:text-2xl lg:text-3xl font-bold hidden sm:block">
                SportProyect
              </h1>
            </div>
            
            <!-- Navigation Desktop -->
            <nav class="hidden lg:flex items-center space-x-6 xl:space-x-8">
              <a *ngFor="let link of navLinks" 
                 [routerLink]="link.path" 
                 class="text-sm xl:text-base font-medium text-muted-foreground hover:text-foreground transition-colors">
                {{ link.label }}
              </a>
            </nav>
            
            <!-- Actions -->
            <div class="flex items-center space-x-2 sm:space-x-4">
              <app-theme-toggle />
              <button class="lg:hidden btn btn-ghost p-2">
                <!-- Hamburger menu -->
                <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
                </svg>
              </button>
            </div>
          </div>
        </div>
      </header>
      
      <!-- Main content área -->
      <main class="container mx-auto px-4 sm:px-6 lg:px-8 py-6 sm:py-8 lg:py-12">
        <router-outlet />
      </main>
      
      <!-- Footer responsivo -->
      <footer class="border-t bg-muted/30 mt-auto">
        <div class="container mx-auto px-4 sm:px-6 lg:px-8 py-6 sm:py-8">
          <div class="flex flex-col sm:flex-row items-center justify-between space-y-4 sm:space-y-0">
            <p class="text-sm text-muted-foreground text-center sm:text-left">
              © 2024 SportProyect. Built with Angular & .NET
            </p>
            <div class="flex space-x-4 sm:space-x-6">
              <a href="/privacy" class="text-sm text-muted-foreground hover:text-foreground transition-colors">
                Privacy
              </a>
              <a href="/terms" class="text-sm text-muted-foreground hover:text-foreground transition-colors">
                Terms
              </a>
            </div>
          </div>
        </div>
      </footer>
    </div>
  `
})
export class MainLayoutComponent {}
```

### 3. Formularios Responsivos
```typescript
@Component({
  template: `
    <form class="space-y-6 sm:space-y-8">
      <!-- Grid responsivo para formularios -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 sm:gap-6">
        <!-- Campo de texto responsivo -->
        <div class="form-group md:col-span-1">
          <label class="form-label text-sm sm:text-base">Team Name</label>
          <input 
            type="text" 
            class="form-control text-sm sm:text-base"
            placeholder="Enter team name"
          >
        </div>
        
        <!-- Campo de select responsivo -->
        <div class="form-group md:col-span-1">
          <label class="form-label text-sm sm:text-base">Division</label>
          <select class="form-control text-sm sm:text-base">
            <option>Primera División</option>
            <option>Segunda División</option>
          </select>
        </div>
        
        <!-- Campo que ocupa toda la fila en mobile -->
        <div class="form-group md:col-span-2 lg:col-span-1">
          <label class="form-label text-sm sm:text-base">Founded Year</label>
          <input 
            type="number" 
            class="form-control text-sm sm:text-base"
            placeholder="1950"
          >
        </div>
      </div>
      
      <!-- Descripción en textarea -->
      <div class="form-group">
        <label class="form-label text-sm sm:text-base">Description</label>
        <textarea 
          class="form-control text-sm sm:text-base h-24 sm:h-32 lg:h-40 resize-none"
          placeholder="Team description..."
        ></textarea>
      </div>
      
      <!-- Botones responsivos -->
      <div class="flex flex-col sm:flex-row justify-end space-y-3 sm:space-y-0 sm:space-x-4">
        <button type="button" class="btn btn-outline w-full sm:w-auto order-2 sm:order-1">
          Cancel
        </button>
        <button type="submit" class="btn btn-primary w-full sm:w-auto order-1 sm:order-2">
          Save Team
        </button>
      </div>
    </form>
  `
})
export class TeamFormComponent {}
```

### 4. Tablas Responsivas
```typescript
@Component({
  template: `
    <!-- Wrapper responsivo para tablas -->
    <div class="overflow-hidden">
      <!-- Modo tabla en desktop -->
      <div class="hidden lg:block">
        <table class="table">
          <thead class="table-header">
            <tr>
              <th class="table-cell text-left">Player</th>
              <th class="table-cell text-center">Position</th>
              <th class="table-cell text-center">Goals</th>
              <th class="table-cell text-center">Assists</th>
              <th class="table-cell text-center">Points</th>
              <th class="table-cell text-right">Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr *ngFor="let player of players" class="table-row">
              <td class="table-cell">
                <div class="flex items-center">
                  <img [src]="player.avatar" class="h-10 w-10 rounded-full mr-3">
                  <div>
                    <div class="font-medium">{{ player.name }}</div>
                    <div class="text-sm text-muted-foreground">#{{ player.number }}</div>
                  </div>
                </div>
              </td>
              <td class="table-cell text-center">{{ player.position }}</td>
              <td class="table-cell text-center font-medium">{{ player.goals }}</td>
              <td class="table-cell text-center font-medium">{{ player.assists }}</td>
              <td class="table-cell text-center font-bold text-primary">{{ player.points }}</td>
              <td class="table-cell text-right">
                <button class="btn btn-ghost btn-sm">
                  <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 12h.01M12 12h.01M19 12h.01" />
                  </svg>
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      
      <!-- Modo cards en mobile/tablet -->
      <div class="block lg:hidden space-y-4">
        <div *ngFor="let player of players" class="card">
          <div class="card-body">
            <!-- Header del card -->
            <div class="flex items-center justify-between mb-4">
              <div class="flex items-center">
                <img [src]="player.avatar" class="h-12 w-12 rounded-full mr-3">
                <div>
                  <div class="font-medium text-base">{{ player.name }}</div>
                  <div class="text-sm text-muted-foreground">{{ player.position }} • #{{ player.number }}</div>
                </div>
              </div>
              <button class="btn btn-ghost btn-sm">
                <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 12h.01M12 12h.01M19 12h.01" />
                </svg>
              </button>
            </div>
            
            <!-- Stats del card -->
            <div class="grid grid-cols-3 gap-4 text-center">
              <div>
                <div class="text-2xl font-bold text-primary">{{ player.goals }}</div>
                <div class="text-sm text-muted-foreground">Goals</div>
              </div>
              <div>
                <div class="text-2xl font-bold text-muted-foreground">{{ player.assists }}</div>
                <div class="text-sm text-muted-foreground">Assists</div>
              </div>
              <div>
                <div class="text-2xl font-bold text-primary">{{ player.points }}</div>
                <div class="text-sm text-muted-foreground">Points</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  `
})
export class PlayersTableComponent {}
```

### 5. Navigation/Menu Responsivo
```typescript
@Component({
  template: `
    <!-- Mobile menu overlay -->
    <div *ngIf="mobileMenuOpen" 
         class="fixed inset-0 z-50 lg:hidden"
         (click)="closeMobileMenu()">
      <!-- Backdrop -->
      <div class="fixed inset-0 bg-black/50 backdrop-blur-sm"></div>
      
      <!-- Menu panel -->
      <div class="fixed inset-y-0 right-0 z-50 w-full max-w-sm bg-background shadow-xl border-l">
        <div class="flex h-16 items-center justify-between px-4 border-b">
          <h2 class="text-lg font-semibold">Menu</h2>
          <button (click)="closeMobileMenu()" class="btn btn-ghost p-2">
            <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </div>
        
        <!-- Menu items -->
        <nav class="px-4 py-6">
          <div class="space-y-2">
            <a *ngFor="let link of navLinks"
               [routerLink]="link.path"
               (click)="closeMobileMenu()"
               class="block px-3 py-2 text-base font-medium rounded-md hover:bg-accent transition-colors">
              {{ link.label }}
            </a>
          </div>
        </nav>
      </div>
    </div>
  `
})
export class MobileMenuComponent {}
```

## Estados y Variantes (OBLIGATORIO usar)

### 1. Estados de Hover y Focus
```html
<!-- Siempre incluir estados hover y focus -->
<button class="btn bg-primary hover:bg-primary-600 focus:ring-2 focus:ring-primary focus:ring-offset-2 active:bg-primary-700">
  Action Button
</button>

<!-- Cards con hover states -->
<div class="card hover:shadow-lg hover:-translate-y-1 transition-all duration-300 cursor-pointer">
  <!-- Contenido -->
</div>
```

### 2. Dark Mode (Usando nuestro sistema de theming)
```html
<!-- Todos los componentes deben soportar dark mode automáticamente -->
<div class="bg-background text-foreground border border-border">
  <!-- Las variables CSS ya manejan el dark mode -->
</div>
```

### 3. Estados de Formularios
```html
<!-- Estados de validación obligatorios -->
<input class="form-control 
             invalid:border-destructive invalid:text-destructive 
             focus:invalid:border-destructive focus:invalid:ring-destructive
             valid:border-green-500
             disabled:opacity-50 disabled:cursor-not-allowed">
```

## Container Queries (Recomendado para componentes complejos)
```html
<!-- Uso de container queries cuando el componente necesita adaptarse a su contenedor -->
<div class="@container">
  <div class="grid @sm:grid-cols-2 @lg:grid-cols-3 gap-4">
    <!-- Items -->
  </div>
</div>
```

## Animaciones y Transiciones Responsivas
```html
<!-- Reducir animaciones en mobile -->
<div class="transition-all duration-300 motion-reduce:transition-none 
           hover:scale-105 sm:hover:scale-110 
           hover:shadow-lg sm:hover:shadow-xl">
  <!-- Contenido animado -->
</div>
```

## Texto Responsivo (Obligatorio)
```html
<!-- Escalas de texto responsive -->
<h1 class="text-2xl sm:text-3xl md:text-4xl lg:text-5xl xl:text-6xl font-bold">
  Main Heading
</h1>

<h2 class="text-xl sm:text-2xl md:text-3xl lg:text-4xl font-semibold">
  Secondary Heading  
</h2>

<p class="text-sm sm:text-base lg:text-lg">
  Body text que se adapta al tamaño de pantalla
</p>
```

## Espaciado Responsivo (Obligatorio)
```html
<!-- Padding responsivo -->
<div class="p-4 sm:p-6 md:p-8 lg:p-12">
  <!-- Contenido -->
</div>

<!-- Margin responsivo -->
<div class="mb-4 sm:mb-6 md:mb-8 lg:mb-12">
  <!-- Contenido -->
</div>

<!-- Gap responsivo en grids -->
<div class="grid grid-cols-1 md:grid-cols-2 gap-4 sm:gap-6 lg:gap-8">
  <!-- Items -->
</div>
```

## Testing Responsivo (Obligatorio)

### 1. Breakpoints a probar siempre:
- **320px**: Mobile portrait (iPhone SE)
- **375px**: Mobile portrait (iPhone)  
- **768px**: Tablet portrait (iPad)
- **1024px**: Laptop (tablet landscape)
- **1280px**: Desktop
- **1536px**: Large desktop

### 2. Testing en componentes:
```typescript
// En cada componente, documenta los breakpoints
@Component({
  // Breakpoints soportados: 
  // Mobile: 320px-640px
  // Tablet: 640px-1024px  
  // Desktop: 1024px+
})
export class ResponsiveComponent {}
```

## Herramientas de Desarrollo (Obligatorias)

### 1. DevTools Extensions
- **Tailwind CSS IntelliSense** (VSCode)
- **Headwind** (para ordenar clases)
- **Tailwind Fold** (para colapsar clases largas)

### 2. Browser DevTools
```javascript
// Script para testing rápido de breakpoints
const testBreakpoints = () => {
  const breakpoints = [320, 375, 640, 768, 1024, 1280, 1536];
  breakpoints.forEach(width => {
    setTimeout(() => {
      window.resizeTo(width, 800);
      console.log(`Testing ${width}px breakpoint`);
    }, breakpoints.indexOf(width) * 2000);
  });
};
```

## Errores Comunes a Evitar

### ❌ NO HACER:
```html
<!-- No usar CSS personalizado para responsive -->
<div style="width: 50%; @media (max-width: 768px) { width: 100%; }">

<!-- No hardcodear tamaños sin responsive -->
<div class="w-64 h-48">

<!-- No usar breakpoints inconsistentes -->
<div class="block sm:hidden md:block lg:hidden">
```

### ✅ HACER:
```html
<!-- Usar utilidades de Tailwind -->
<div class="w-full md:w-1/2">

<!-- Responsive desde el inicio -->
<div class="w-full sm:w-64 h-32 sm:h-48">

<!-- Breakpoints lógicos y consistentes -->
<div class="block lg:hidden">
```

## Checklist de Componente Responsivo ✅

Antes de considerar completo cualquier componente Angular, verificar:

- [ ] **Mobile-first**: Funciona en 320px
- [ ] **Breakpoints**: Probado en todos los breakpoints (sm, md, lg, xl, 2xl)
- [ ] **Touch targets**: Botones mínimo 44px en mobile
- [ ] **Texto legible**: Tamaño mínimo 16px en mobile
- [ ] **Imágenes**: Aspect ratio mantenido en todos los tamaños
- [ ] **Navegación**: Funcional en todas las pantallas
- [ ] **Formularios**: Usables en mobile
- [ ] **Tablas**: Alternativa para mobile (cards/stacked)
- [ ] **Estados hover**: Solo en dispositivos con hover
- [ ] **Dark mode**: Funcional automáticamente
- [ ] **Performance**: Sin scroll horizontal en ningún breakpoint

## Documentación Adicional

Para casos específicos no cubiertos en esta guía, consultar:
- [Documentación oficial de Tailwind](https://tailwindcss.com/docs)
- Archivo `C:\Proyectos\AgentOs\TailwindcssDoc.md` (documentación completa)

---

**Esta guía es de cumplimiento OBLIGATORIO para todos los componentes Angular en SportProyect. No se aceptarán PRs que no sigan estas normas de diseño responsivo.**
