# Sistema de Theming OKLCH para SportProyect

## Implementación del Sistema de Theming Completo

Este sistema permite cambio dinámico entre modo claro y oscuro usando variables CSS OKLCH y controles de TypeScript/Angular.

### Angular Theme Service

```typescript
// src/app/core/services/theme.service.ts
import { Injectable, signal, effect, PLATFORM_ID, inject } from '@angular/core';
import { isPlatformBrowser } from '@angular/common';

export type Theme = 'light' | 'dark' | 'system';

@Injectable({
  providedIn: 'root'
})
export class ThemeService {
  private readonly platformId = inject(PLATFORM_ID);
  private readonly storageKey = 'sportproyect-theme';
  
  // Signals for reactive theme management
  theme = signal<Theme>('system');
  systemPreference = signal<'light' | 'dark'>('light');
  
  // Computed actual theme (resolves 'system' to actual preference)
  actualTheme = signal<'light' | 'dark'>('light');
  
  constructor() {
    if (isPlatformBrowser(this.platformId)) {
      this.initializeTheme();
      this.listenToSystemChanges();
    }
    
    // Effect to update DOM when theme changes
    effect(() => {
      if (isPlatformBrowser(this.platformId)) {
        this.applyTheme(this.actualTheme());
      }
    });
  }
  
  private initializeTheme(): void {
    // Get saved theme from localStorage
    const saved = localStorage.getItem(this.storageKey) as Theme;
    if (saved && ['light', 'dark', 'system'].includes(saved)) {
      this.theme.set(saved);
    }
    
    // Get system preference
    this.updateSystemPreference();
    
    // Set initial actual theme
    this.updateActualTheme();
  }
  
  private listenToSystemChanges(): void {
    const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
    mediaQuery.addEventListener('change', () => {
      this.updateSystemPreference();
      if (this.theme() === 'system') {
        this.updateActualTheme();
      }
    });
  }
  
  private updateSystemPreference(): void {
    const isDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
    this.systemPreference.set(isDark ? 'dark' : 'light');
  }
  
  private updateActualTheme(): void {
    const theme = this.theme();
    if (theme === 'system') {
      this.actualTheme.set(this.systemPreference());
    } else {
      this.actualTheme.set(theme);
    }
  }
  
  private applyTheme(theme: 'light' | 'dark'): void {
    const root = document.documentElement;
    
    // Remove existing theme classes
    root.classList.remove('light', 'dark');
    
    // Add new theme class with smooth transition
    root.style.setProperty('transition', 'background-color 0.3s ease, color 0.3s ease');
    root.classList.add(theme);
    
    // Remove transition after animation completes
    setTimeout(() => {
      root.style.removeProperty('transition');
    }, 300);
  }
  
  // Public API
  setTheme(theme: Theme): void {
    this.theme.set(theme);
    this.updateActualTheme();
    
    if (isPlatformBrowser(this.platformId)) {
      localStorage.setItem(this.storageKey, theme);
    }
  }
  
  toggleTheme(): void {
    const current = this.theme();
    if (current === 'light') {
      this.setTheme('dark');
    } else if (current === 'dark') {
      this.setTheme('light');
    } else {
      // If system, toggle to opposite of current system preference
      const opposite = this.systemPreference() === 'light' ? 'dark' : 'light';
      this.setTheme(opposite);
    }
  }
  
  isDark(): boolean {
    return this.actualTheme() === 'dark';
  }
  
  getThemeIcon(): string {
    const theme = this.theme();
    switch (theme) {
      case 'light':
        return 'sun';
      case 'dark':
        return 'moon';
      case 'system':
        return 'monitor';
      default:
        return 'monitor';
    }
  }
  
  getThemeLabel(): string {
    const theme = this.theme();
    switch (theme) {
      case 'light':
        return 'Light Mode';
      case 'dark':
        return 'Dark Mode';
      case 'system':
        return 'System Default';
      default:
        return 'System Default';
    }
  }
}
```

### Theme Toggle Component

```typescript
// src/app/shared/components/theme-toggle/theme-toggle.component.ts
import { Component, inject, signal } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ThemeService, type Theme } from '../../../core/services/theme.service';

@Component({
  selector: 'app-theme-toggle',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div class="theme-toggle-container">
      <!-- Simple Toggle Button -->
      <button 
        class="theme-toggle"
        (click)="toggleTheme()"
        [attr.aria-label]="getAriaLabel()"
        title="Toggle theme"
      >
        <svg 
          class="theme-icon transition-transform duration-300"
          [class.rotate-180]="themeService.isDark()"
          width="20" 
          height="20" 
          viewBox="0 0 24 24" 
          fill="none" 
          stroke="currentColor" 
          stroke-width="2"
        >
          <!-- Sun icon (visible in light mode) -->
          <g *ngIf="!themeService.isDark()">
            <circle cx="12" cy="12" r="5"/>
            <path d="m12 7v-2m0 14v-2m5-5h2m-14 0h-2m12.12-5.12l1.42-1.42M5.46 5.46l1.42 1.42m0 11.12l-1.42 1.42m12.12 0l-1.42-1.42"/>
          </g>
          
          <!-- Moon icon (visible in dark mode) -->
          <g *ngIf="themeService.isDark()">
            <path d="M12 3a6.364 6.364 0 0 0 9 9 9 9 0 1 1-9-9Z"/>
          </g>
        </svg>
      </button>
      
      <!-- Advanced Dropdown (optional) -->
      <div class="theme-dropdown" *ngIf="showAdvanced()">
        <button 
          class="theme-dropdown-button"
          (click)="toggleDropdown()"
        >
          <span>{{ themeService.getThemeLabel() }}</span>
          <svg 
            class="dropdown-arrow transition-transform duration-200"
            [class.rotate-180]="isDropdownOpen()"
            width="16" 
            height="16" 
            viewBox="0 0 24 24" 
            fill="none" 
            stroke="currentColor" 
            stroke-width="2"
          >
            <path d="M6 9l6 6 6-6"/>
          </svg>
        </button>
        
        <div 
          class="dropdown-menu"
          [class.show]="isDropdownOpen()"
        >
          <button 
            *ngFor="let option of themeOptions"
            class="dropdown-item"
            [class.active]="themeService.theme() === option.value"
            (click)="selectTheme(option.value)"
          >
            <svg class="dropdown-icon" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <g [ngSwitch]="option.value">
                <!-- Light mode icon -->
                <g *ngSwitchCase="'light'">
                  <circle cx="12" cy="12" r="5"/>
                  <path d="m12 7v-2m0 14v-2m5-5h2m-14 0h-2m12.12-5.12l1.42-1.42M5.46 5.46l1.42 1.42m0 11.12l-1.42 1.42m12.12 0l-1.42-1.42"/>
                </g>
                
                <!-- Dark mode icon -->
                <g *ngSwitchCase="'dark'">
                  <path d="M12 3a6.364 6.364 0 0 0 9 9 9 9 0 1 1-9-9Z"/>
                </g>
                
                <!-- System mode icon -->
                <g *ngSwitchCase="'system'">
                  <rect x="2" y="3" width="20" height="14" rx="2" ry="2"/>
                  <line x1="8" y1="21" x2="16" y2="21"/>
                  <line x1="12" y1="17" x2="12" y2="21"/>
                </g>
              </g>
            </svg>
            <span>{{ option.label }}</span>
            <svg 
              *ngIf="themeService.theme() === option.value"
              class="check-icon"
              width="16" 
              height="16" 
              viewBox="0 0 24 24" 
              fill="none" 
              stroke="currentColor" 
              stroke-width="2"
            >
              <polyline points="20,6 9,17 4,12"/>
            </svg>
          </button>
        </div>
      </div>
    </div>
  `,
  styles: [`
    .theme-toggle-container {
      position: relative;
      display: inline-block;
    }
    
    .theme-toggle {
      @apply p-2;
      background-color: var(--secondary);
      color: var(--secondary-foreground);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      cursor: pointer;
      transition: all 0.3s ease;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    
    .theme-toggle:hover {
      background-color: var(--accent);
      color: var(--accent-foreground);
      box-shadow: var(--shadow-md);
    }
    
    .theme-toggle:focus {
      outline: none;
      box-shadow: 0 0 0 2px var(--ring);
    }
    
    .theme-icon {
      width: 20px;
      height: 20px;
    }
    
    .theme-dropdown-button {
      @apply flex items-center justify-between px-3 py-2 text-sm;
      background-color: var(--secondary);
      color: var(--secondary-foreground);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      min-width: 140px;
      cursor: pointer;
      transition: all 0.2s ease;
    }
    
    .theme-dropdown-button:hover {
      background-color: var(--accent);
    }
    
    .dropdown-menu {
      position: absolute;
      top: calc(100% + 4px);
      right: 0;
      background-color: var(--popover);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      box-shadow: var(--shadow-lg);
      min-width: 160px;
      opacity: 0;
      visibility: hidden;
      transform: translateY(-4px);
      transition: all 0.2s ease;
      z-index: 1000;
    }
    
    .dropdown-menu.show {
      opacity: 1;
      visibility: visible;
      transform: translateY(0);
    }
    
    .dropdown-item {
      @apply flex items-center gap-3 w-full px-3 py-2 text-sm text-left;
      color: var(--popover-foreground);
      cursor: pointer;
      transition: background-color 0.2s ease;
      border: none;
      background: none;
    }
    
    .dropdown-item:hover {
      background-color: var(--accent);
      color: var(--accent-foreground);
    }
    
    .dropdown-item.active {
      background-color: var(--primary);
      color: var(--primary-foreground);
    }
    
    .dropdown-icon, .check-icon {
      width: 16px;
      height: 16px;
      flex-shrink: 0;
    }
    
    .check-icon {
      margin-left: auto;
    }
  `]
})
export class ThemeToggleComponent {
  readonly themeService = inject(ThemeService);
  
  private readonly isDropdownOpen = signal(false);
  private readonly showAdvanced = signal(false);
  
  readonly themeOptions: Array<{ value: Theme; label: string }> = [
    { value: 'light', label: 'Light Mode' },
    { value: 'dark', label: 'Dark Mode' },
    { value: 'system', label: 'System Default' }
  ];
  
  toggleTheme(): void {
    this.themeService.toggleTheme();
  }
  
  toggleDropdown(): void {
    this.isDropdownOpen.update(open => !open);
  }
  
  selectTheme(theme: Theme): void {
    this.themeService.setTheme(theme);
    this.isDropdownOpen.set(false);
  }
  
  getAriaLabel(): string {
    const current = this.themeService.actualTheme();
    const next = current === 'light' ? 'dark' : 'light';
    return `Switch to ${next} mode`;
  }
}
```

### Layout Integration

```typescript
// src/app/layouts/main-layout/main-layout.component.ts
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterOutlet } from '@angular/router';
import { ThemeToggleComponent } from '../../shared/components/theme-toggle/theme-toggle.component';

@Component({
  selector: 'app-main-layout',
  standalone: true,
  imports: [CommonModule, RouterOutlet, ThemeToggleComponent],
  template: `
    <div class="min-h-screen transition-colors duration-300">
      <!-- Header -->
      <header class="sticky top-0 z-50">
        <nav class="sidebar border-b">
          <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex justify-between items-center h-16">
              <!-- Logo -->
              <div class="flex items-center">
                <img 
                  src="/assets/logo.svg" 
                  alt="SportProyect" 
                  class="h-8 w-auto"
                >
                <h1 class="ml-3 text-xl font-bold">
                  SportProyect
                </h1>
              </div>
              
              <!-- Navigation -->
              <div class="hidden md:flex items-center space-x-8">
                <a 
                  href="/dashboard" 
                  class="sidebar-nav-item text-sm font-medium"
                >
                  Dashboard
                </a>
                <a 
                  href="/teams" 
                  class="sidebar-nav-item text-sm font-medium"
                >
                  Teams
                </a>
                <a 
                  href="/matches" 
                  class="sidebar-nav-item text-sm font-medium"
                >
                  Matches
                </a>
                <a 
                  href="/players" 
                  class="sidebar-nav-item text-sm font-medium"
                >
                  Players
                </a>
              </div>
              
              <!-- Theme Toggle -->
              <div class="flex items-center space-x-4">
                <app-theme-toggle />
              </div>
            </div>
          </div>
        </nav>
      </header>
      
      <!-- Main Content -->
      <main class="flex-1">
        <router-outlet />
      </main>
      
      <!-- Footer -->
      <footer class="border-t">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
          <div class="text-center text-sm">
            <p class="text-muted-foreground">
              © 2024 SportProyect. Built with Angular & .NET
            </p>
          </div>
        </div>
      </footer>
    </div>
  `
})
export class MainLayoutComponent {}
```

### App Component Setup

```typescript
// src/app/app.component.ts
import { Component, OnInit, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterOutlet } from '@angular/router';
import { ThemeService } from './core/services/theme.service';
import { MainLayoutComponent } from './layouts/main-layout/main-layout.component';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [CommonModule, RouterOutlet, MainLayoutComponent],
  template: `
    <app-main-layout>
      <router-outlet />
    </app-main-layout>
  `,
  styles: [`
    :host {
      display: block;
      min-height: 100vh;
    }
  `]
})
export class AppComponent implements OnInit {
  private readonly themeService = inject(ThemeService);
  
  ngOnInit(): void {
    // Theme service initializes automatically
    console.log('App initialized with theme:', this.themeService.actualTheme());
  }
}
```

### Usage Examples

```typescript
// Uso en cualquier componente
export class SomeComponent {
  private readonly themeService = inject(ThemeService);
  
  // Reactive access to theme
  isDark = computed(() => this.themeService.isDark());
  currentTheme = computed(() => this.themeService.actualTheme());
  
  // Methods
  toggleTheme() {
    this.themeService.toggleTheme();
  }
  
  setSpecificTheme() {
    this.themeService.setTheme('dark');
  }
}
```

### HTML Usage Patterns

```html
<!-- Conditional rendering based on theme -->
<div [class.dark-specific]="isDark()">
  <p>This content adapts to theme</p>
</div>

<!-- Using CSS variables directly -->
<div style="background-color: var(--primary); color: var(--primary-foreground);">
  Theme-aware content
</div>

<!-- Classes that automatically adapt -->
<button class="btn btn-primary">
  Primary Button (adapts automatically)
</button>

<div class="card">
  <div class="card-header">
    <h3>Theme-aware Card</h3>
  </div>
  <div class="card-body">
    <p>Content that changes with theme</p>
  </div>
</div>
```

## Advanced Features

### Theme Persistence Across Sessions

El `ThemeService` automáticamente:
- Guarda la preferencia en `localStorage`
- Restaura la preferencia al cargar la aplicación
- Respeta las preferencias del sistema operativo
- Escucha cambios en las preferencias del sistema

### Smooth Transitions

```css
/* Agregado automáticamente por el ThemeService */
html {
  transition: background-color 0.3s ease, color 0.3s ease;
}

/* Todas las variables CSS también tienen transiciones suaves */
* {
  transition: background-color 0.3s ease, border-color 0.3s ease, color 0.3s ease;
}
```

### Theme-aware Components

```typescript
// Ejemplo de componente que reacciona al tema
@Component({
  template: `
    <div class="chart-container">
      <canvas 
        #canvas
        [style.background-color]="chartBackground()"
      ></canvas>
    </div>
  `
})
export class ChartComponent {
  private readonly themeService = inject(ThemeService);
  
  chartBackground = computed(() => 
    this.themeService.isDark() ? 'var(--card)' : 'var(--background)'
  );
  
  // Actualizar colores de gráfico basándose en el tema
  @effect
  updateChartColors = effect(() => {
    if (this.chart) {
      this.chart.options.scales.x.grid.color = 'var(--border)';
      this.chart.options.scales.y.grid.color = 'var(--border)';
      this.chart.update();
    }
  });
}
```

## Best Practices

1. **Usa las variables CSS**: Siempre prefiere `var(--primary)` sobre colores hardcodeados
2. **Signals for reactivity**: Usa computed signals para reactividad eficiente
3. **Consistent transitions**: Todas las transiciones deben ser de 300ms para consistencia
4. **Test both themes**: Siempre prueba tu UI en ambos modos
5. **Respect system preferences**: El modo 'system' debe ser el default
6. **Accessibility**: Asegúrate de que el contraste sea adecuado en ambos temas
7. **Performance**: Las variables CSS son más performantes que cambios de clase
8. **User preference**: Siempre persiste la preferencia del usuario

## Theme Testing

```typescript
// Para testing
describe('ThemeService', () => {
  let service: ThemeService;
  
  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(ThemeService);
  });
  
  it('should toggle between light and dark', () => {
    service.setTheme('light');
    expect(service.actualTheme()).toBe('light');
    
    service.toggleTheme();
    expect(service.actualTheme()).toBe('dark');
  });
  
  it('should persist theme preference', () => {
    service.setTheme('dark');
    // Simulate page reload
    const newService = TestBed.inject(ThemeService);
    expect(newService.theme()).toBe('dark');
  });
});
```
