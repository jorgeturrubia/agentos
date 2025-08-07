# Angular 18+ Style Guide para SportProyect

## ⚠️ NORMA OBLIGATORIA: USO DE TAILWINDCSS

**REGLA FUNDAMENTAL: TODOS los componentes Angular DEBEN usar exclusivamente TailwindCSS para diseño responsivo.**

- ✅ **SIEMPRE** consultar: `tailwind-responsive-design-style.md`
- ❌ **NUNCA** usar CSS personalizado para responsive design
- ⚠️ **OBLIGATORIO** seguir patrones mobile-first de Tailwind
- 📖 **REFERENCIA COMPLETA**: `C:\Proyectos\AgentOs\TailwindcssDoc.md`

## Configuración del Proyecto

### Integración con TailwindCSS 4

Para integrar correctamente Tailwind 4 con Angular, sigue esta configuración:

#### angular.json

```json
{
  "projects": {
    "sport-proyect": {
      "architect": {
        "build": {
          "options": {
            "styles": [
              "src/styles.scss"
            ]
          }
        }
      }
    }
  }
}
```

#### src/styles.scss

```scss
// Import Tailwind 4
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities';

// Aquí se cargan automáticamente las clases definidas en css-tailwind-style.md
@layer components {
  // Los componentes se definen en css-tailwind-style.md
  // Este archivo debe ser importado o las clases incluidas aquí
  
  // Ejemplo de uso de clases base:
  .btn-secondary {
    @apply btn text-gray-700 bg-gray-200 hover:bg-gray-300 focus:ring-gray-500;
  }
  
  .form-control {
    @apply block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500;
  }
  
  .card {
    @apply bg-white rounded-lg shadow-md p-6 border border-gray-200;
  }
  
  .dark .card {
    @apply bg-gray-800 border-gray-700;
  }
}

@layer utilities {
  .animate-fade-in {
    animation: fadeIn 0.3s ease-in-out;
  }
  
  @keyframes fadeIn {
    from {
      opacity: 0;
      transform: translateY(10px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
}

// Global styles específicos de Angular
* {
  box-sizing: border-box;
}

html, body {
  height: 100%;
  margin: 0;
  font-family: theme('fontFamily.sans');
}

// Angular Material override si es necesario
.mat-mdc-snack-bar-container {
  @apply !bg-gray-800 !text-white;
}

.error-snackbar {
  @apply !bg-red-600;
}
```

#### tailwind.config.ts

```typescript
import type { Config } from 'tailwindcss';

export default {
  content: [
    "./src/**/*.{html,ts}",
    "./src/**/*.component.ts",
  ],
  darkMode: 'class',
  theme: {
    extend: {
      // Ver detalles completos en css-tailwind-style.md
      colors: {
        primary: {
          500: '#3b82f6',
          600: '#2563eb',
          // Ver paleta completa en css-tailwind-style.md
        }
      }
    }
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
    require('@tailwindcss/container-queries'),
  ],
} satisfies Config;
```

## Estructura de Componentes Angular con Tailwind

### Standalone Component Pattern

```typescript
import { Component, signal, computed, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterModule } from '@angular/router';

@Component({
  selector: 'app-team-card',
  standalone: true,
  imports: [CommonModule, RouterModule],
  template: `
    <!-- Usando clases del css-tailwind-style.md -->
    <div class="card hover:shadow-xl transition-shadow duration-300 group">
      <!-- Header con imagen de fondo -->
      <div class="relative h-48 overflow-hidden rounded-t-lg">
        <img 
          [src]="team().logoUrl" 
          [alt]="team().name + ' logo'"
          class="w-full h-full object-cover transition-transform duration-300 group-hover:scale-105"
          loading="lazy"
        >
        <!-- Badge de división -->
        <div class="absolute top-4 right-4">
          <span class="badge badge-primary">{{ team().division }}</span>
        </div>
      </div>
      
      <!-- Content -->
      <div class="card-body">
        <h3 class="text-xl font-bold text-gray-900 dark:text-white mb-2">
          {{ team().name }}
        </h3>
        
        <p class="text-gray-600 dark:text-gray-400 mb-4">
          {{ team().city }}, {{ team().country }}
        </p>
        
        <!-- Stats grid -->
        <div class="grid grid-cols-3 gap-4 mb-4">
          <div class="text-center">
            <div class="text-2xl font-bold text-primary-600">{{ team().stats.wins }}</div>
            <div class="text-sm text-gray-500">Wins</div>
          </div>
          <div class="text-center">
            <div class="text-2xl font-bold text-gray-600">{{ team().stats.draws }}</div>
            <div class="text-sm text-gray-500">Draws</div>
          </div>
          <div class="text-center">
            <div class="text-2xl font-bold text-red-600">{{ team().stats.losses }}</div>
            <div class="text-sm text-gray-500">Losses</div>
          </div>
        </div>
        
        <!-- Action buttons -->
        <div class="flex gap-2 mt-6">
          <button 
            class="btn btn-primary flex-1"
            (click)="viewDetails(team().id)"
          >
            View Details
          </button>
          <button 
            class="btn btn-outline"
            (click)="toggleFavorite(team().id)"
            [class.btn-primary]="isFavorite()"
          >
            <svg class="w-4 h-4" [class.fill-current]="isFavorite()" viewBox="0 0 20 20">
              <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z"/>
            </svg>
          </button>
        </div>
      </div>
    </div>
  `,
  styles: [
    // Solo estilos que NO se pueden hacer con Tailwind
    `
      :host {
        display: block;
        transition: transform 0.2s ease;
      }
      
      :host:hover {
        transform: translateY(-2px);
      }
    `
  ]
})
export class TeamCardComponent {
  // Signals para estado reactivo
  team = signal<Team>({
    id: '',
    name: '',
    city: '',
    country: '',
    division: '',
    logoUrl: '',
    stats: { wins: 0, draws: 0, losses: 0 }
  });
  
  favoriteTeams = signal<string[]>([]);
  
  // Computed para derivar estado
  isFavorite = computed(() => 
    this.favoriteTeams().includes(this.team().id)
  );
  
  // Métodos de acción
  viewDetails(teamId: string): void {
    // Navigation logic
    console.log('Viewing details for team:', teamId);
  }
  
  toggleFavorite(teamId: string): void {
    const favorites = this.favoriteTeams();
    if (favorites.includes(teamId)) {
      this.favoriteTeams.set(favorites.filter(id => id !== teamId));
    } else {
      this.favoriteTeams.set([...favorites, teamId]);
    }
  }
}

interface Team {
  id: string;
  name: string;
  city: string;
  country: string;
  division: string;
  logoUrl: string;
  stats: {
    wins: number;
    draws: number;
    losses: number;
  };
}
```

## Error Handling Service

```typescript
import { Injectable, inject } from '@angular/core';
import { Router } from '@angular/router';
import { MatSnackBar } from '@angular/material/snack-bar';

@Injectable({
  providedIn: 'root'
})
export class ErrorHandlerService {
  private readonly router = inject(Router);
  private readonly snackBar = inject(MatSnackBar);
  
  handleError(error: any): void {
    console.error('Error occurred:', error);
    
    let message = 'An unexpected error occurred';
    
    if (error.status === 401) {
      message = 'Your session has expired. Please login again.';
      this.router.navigate(['/auth/login']);
    } else if (error.status === 403) {
      message = 'You do not have permission to perform this action.';
    } else if (error.status === 404) {
      message = 'The requested resource was not found.';
    } else if (error.status === 422) {
      message = this.extractValidationErrors(error);
    } else if (error.status >= 500) {
      message = 'A server error occurred. Please try again later.';
    } else if (error.message) {
      message = error.message;
    }
    
    this.showError(message);
  }
  
  private extractValidationErrors(error: any): string {
    if (error.error?.errors) {
      const errors = Object.values(error.error.errors).flat();
      return errors.join(', ');
    }
    return 'Validation failed. Please check your input.';
  }
  
  private showError(message: string): void {
    this.snackBar.open(message, 'Close', {
      duration: 5000,
      panelClass: ['error-snackbar'],
      horizontalPosition: 'end',
      verticalPosition: 'top'
    });
  }
}
```

## Performance Optimization

### Virtual Scrolling para Listas Grandes

```typescript
import { Component } from '@angular/core';
import { ScrollingModule } from '@angular/cdk/scrolling';

@Component({
  selector: 'app-player-list',
  standalone: true,
  imports: [CommonModule, ScrollingModule],
  template: `
    <cdk-virtual-scroll-viewport itemSize="80" class="player-viewport">
      <div *cdkVirtualFor="let player of players; trackBy: trackByPlayerId" class="player-item">
        <img [src]="player.avatar" [alt]="player.name" class="player-avatar" />
        <div class="player-info">
          <h3>{{ player.name }}</h3>
          <p>{{ player.position }} - #{{ player.number }}</p>
        </div>
      </div>
    </cdk-virtual-scroll-viewport>
  `,
  styles: [`
    .player-viewport {
      height: 600px;
      width: 100%;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
    
    .player-item {
      display: flex;
      align-items: center;
      padding: 1rem;
      border-bottom: 1px solid #eee;
    }
    
    .player-avatar {
      width: 50px;
      height: 50px;
      border-radius: 50%;
      margin-right: 1rem;
    }
  `]
})
export class PlayerListComponent {
  players = signal<Player[]>([]);
  
  trackByPlayerId(index: number, player: Player): string {
    return player.id;
  }
}
```

### Lazy Loading de Imágenes

```typescript
import { Directive, ElementRef, Input, OnInit, inject } from '@angular/core';

@Directive({
  selector: 'img[appLazyLoad]',
  standalone: true
})
export class LazyLoadDirective implements OnInit {
  @Input() appLazyLoad!: string;
  private readonly el = inject(ElementRef);
  
  ngOnInit() {
    const img = this.el.nativeElement as HTMLImageElement;
    
    // Set placeholder
    img.src = 'assets/images/placeholder.jpg';
    
    // Create observer
    const observer = new IntersectionObserver(entries => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          img.src = this.appLazyLoad;
          observer.unobserve(img);
        }
      });
    });
    
    observer.observe(img);
  }
}
```

## Animaciones

```typescript
import { Component } from '@angular/core';
import { trigger, state, style, transition, animate, query, stagger } from '@angular/animations';

@Component({
  selector: 'app-match-list',
  standalone: true,
  animations: [
    trigger('listAnimation', [
      transition('* <=> *', [
        query(':enter', [
          style({ opacity: 0, transform: 'translateY(20px)' }),
          stagger('50ms', [
            animate('300ms ease-out', 
              style({ opacity: 1, transform: 'translateY(0)' })
            )
          ])
        ], { optional: true }),
        query(':leave', [
          animate('300ms ease-out', 
            style({ opacity: 0, transform: 'translateX(-100%)' })
          )
        ], { optional: true })
      ])
    ]),
    trigger('expandCollapse', [
      state('collapsed', style({ height: '0px', overflow: 'hidden' })),
      state('expanded', style({ height: '*' })),
      transition('collapsed <=> expanded', animate('300ms ease-in-out'))
    ])
  ],
  template: `
    <div class="match-list" [@listAnimation]="matches().length">
      @for (match of matches(); track match.id) {
        <div class="match-card" (click)="toggleDetails(match.id)">
          <div class="match-header">
            <span>{{ match.homeTeam }} vs {{ match.awayTeam }}</span>
            <span>{{ match.date | date:'short' }}</span>
          </div>
          <div [@expandCollapse]="getExpandState(match.id)" class="match-details">
            <p>Stadium: {{ match.stadium }}</p>
            <p>Referee: {{ match.referee }}</p>
          </div>
        </div>
      }
    </div>
  `
})
export class MatchListComponent {
  matches = signal<Match[]>([]);
  expandedMatches = new Set<string>();
  
  toggleDetails(matchId: string) {
    if (this.expandedMatches.has(matchId)) {
      this.expandedMatches.delete(matchId);
    } else {
      this.expandedMatches.add(matchId);
    }
  }
  
  getExpandState(matchId: string): 'expanded' | 'collapsed' {
    return this.expandedMatches.has(matchId) ? 'expanded' : 'collapsed';
  }
}
```

## PWA Configuration

```typescript
// app.component.ts
import { Component, OnInit, inject } from '@angular/core';
import { SwUpdate } from '@angular/service-worker';

@Component({
  selector: 'app-root',
  template: `
    <router-outlet />
    
    @if (updateAvailable()) {
      <div class="update-notification">
        <p>A new version is available!</p>
        <button (click)="updateApp()" class="btn btn-primary">
          Update Now
        </button>
      </div>
    }
  `
})
export class AppComponent implements OnInit {
  private readonly swUpdate = inject(SwUpdate);
  updateAvailable = signal(false);
  
  ngOnInit() {
    if (this.swUpdate.isEnabled) {
      this.swUpdate.versionUpdates.subscribe(event => {
        if (event.type === 'VERSION_READY') {
          this.updateAvailable.set(true);
        }
      });
      
      // Check for updates every 30 seconds
      interval(30000).subscribe(() => {
        this.swUpdate.checkForUpdate();
      });
    }
  }
  
  updateApp() {
    this.swUpdate.activateUpdate().then(() => {
      document.location.reload();
    });
  }
}
```

## i18n Configuration

```typescript
import { Component, inject } from '@angular/core';
import { TranslateService } from '@ngx-translate/core';

@Component({
  selector: 'app-language-selector',
  standalone: true,
  template: `
    <select (change)="changeLanguage($event)" [value]="currentLang()">
      <option value="en">English</option>
      <option value="es">Español</option>
      <option value="fr">Français</option>
    </select>
  `
})
export class LanguageSelectorComponent {
  private readonly translate = inject(TranslateService);
  currentLang = signal(this.translate.currentLang || 'en');
  
  constructor() {
    // Set default language
    this.translate.setDefaultLang('en');
    
    // Use browser language if available
    const browserLang = this.translate.getBrowserLang();
    this.translate.use(browserLang?.match(/en|es|fr/) ? browserLang : 'en');
  }
  
  changeLanguage(event: Event) {
    const lang = (event.target as HTMLSelectElement).value;
    this.translate.use(lang);
    this.currentLang.set(lang);
    
    // Save preference
    localStorage.setItem('preferred-language', lang);
  }
}
```

## State Management con SignalStore

```typescript
import { computed, inject } from '@angular/core';
import { signalStore, withState, withComputed, withMethods, patchState } from '@ngrx/signals';
import { TeamService } from './team.service';

interface TeamState {
  teams: Team[];
  selectedTeam: Team | null;
  loading: boolean;
  error: string | null;
  filters: {
    city: string;
    division: string;
  };
}

export const TeamStore = signalStore(
  { providedIn: 'root' },
  withState<TeamState>({
    teams: [],
    selectedTeam: null,
    loading: false,
    error: null,
    filters: {
      city: '',
      division: ''
    }
  }),
  withComputed((store) => ({
    filteredTeams: computed(() => {
      const { teams, filters } = store;
      return teams().filter(team => {
        const cityMatch = !filters.city() || 
          team.city.toLowerCase().includes(filters.city().toLowerCase());
        const divisionMatch = !filters.division() || 
          team.division === filters.division();
        return cityMatch && divisionMatch;
      });
    }),
    totalTeams: computed(() => store.teams().length),
    hasError: computed(() => store.error() !== null)
  })),
  withMethods((store) => {
    const teamService = inject(TeamService);
    
    return {
      async loadTeams() {
        patchState(store, { loading: true, error: null });
        
        try {
          const teams = await teamService.getTeams();
          patchState(store, { teams, loading: false });
        } catch (error) {
          patchState(store, { 
            error: 'Failed to load teams', 
            loading: false 
          });
        }
      },
      
      selectTeam(team: Team) {
        patchState(store, { selectedTeam: team });
      },
      
      updateFilters(filters: Partial<TeamState['filters']>) {
        patchState(store, (state) => ({
          filters: { ...state.filters, ...filters }
        }));
      },
      
      clearFilters() {
        patchState(store, { 
          filters: { city: '', division: '' } 
        });
      }
    };
  })
);
```

## Convenciones de Nomenclatura

- **Componentes**: PascalCase con sufijo Component - `TeamListComponent`
- **Servicios**: PascalCase con sufijo Service - `AuthService`
- **Directivas**: PascalCase con sufijo Directive - `HighlightDirective`
- **Pipes**: PascalCase con sufijo Pipe - `TimeAgoPipe`
- **Interfaces**: PascalCase con prefijo I opcional - `Team` o `ITeam`
- **Archivos**: kebab-case - `team-list.component.ts`
- **Carpetas**: kebab-case - `shared-components`
- **Rutas**: kebab-case - `/team-management`
- **CSS Classes**: kebab-case - `team-card`
- **Métodos**: camelCase - `loadTeams()`, `onTeamSelected()`
- **Propiedades**: camelCase - `selectedTeam`, `isLoading`

## Mejores Prácticas Específicas

1. **Use Standalone Components** por defecto (Angular 20+)
2. **Prefiera Signals** sobre Observables para estado local
3. **Use inject()** en lugar de constructor injection
4. **Implemente OnPush** change detection strategy
5. **Use track functions** en @for loops para optimización
6. **Lazy load** todas las rutas feature
7. **Mantenga componentes pequeños** (< 200 líneas)
8. **Use TypeScript strict mode** siempre
9. **Implemente error boundaries** para manejo robusto
10. **Use Virtual Scrolling** para listas grandes
11. **Prefiera Reactive Forms** sobre Template-driven
12. **Implemente skeleton screens** para mejor UX
13. **Use CDK** para comportamientos complejos
14. **Mantenga el bundle size** < 500KB inicial
15. **Use Web Workers** para operaciones pesadas