# Angular 18 Style Guide - CarozProject

## Standalone Components Architecture

### Project Structure
```
src/
├── app/
│   ├── core/                           # Singleton services, guards, interceptors
│   │   ├── guards/
│   │   ├── interceptors/
│   │   ├── services/
│   │   └── models/
│   ├── shared/                         # Reusable components, pipes, directives
│   │   ├── components/
│   │   ├── pipes/
│   │   ├── directives/
│   │   └── utils/
│   ├── features/                       # Feature modules as standalone components
│   │   ├── purchase-orders/
│   │   │   ├── components/
│   │   │   ├── services/
│   │   │   └── models/
│   │   └── suppliers/
│   └── layouts/                        # Layout components
├── assets/                             # Static assets
├── environments/                       # Environment configurations
└── styles/                            # Global styles
```

## Standalone Component Structure

### Component Definition
```typescript
// ✅ CORRECTO: Standalone component with all imports
import { Component, Input, Output, EventEmitter, OnInit, OnDestroy, inject, signal } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule, FormBuilder, FormGroup, Validators } from '@angular/forms';
import { MatCardModule } from '@angular/material/card';
import { MatButtonModule } from '@angular/material/button';
import { MatInputModule } from '@angular/material/input';
import { MatSelectModule } from '@angular/material/select';
import { Observable, Subject, takeUntil } from 'rxjs';

@Component({
  selector: 'app-purchase-order-form',
  standalone: true,
  imports: [
    CommonModule,
    ReactiveFormsModule,
    MatCardModule,
    MatButtonModule,
    MatInputModule,
    MatSelectModule
  ],
  templateUrl: './purchase-order-form.component.html',
  styleUrls: ['./purchase-order-form.component.scss']
})
export class PurchaseOrderFormComponent implements OnInit, OnDestroy {
  // ✅ Dependency Injection with inject()
  private fb = inject(FormBuilder);
  private purchaseOrderService = inject(PurchaseOrderService);
  private router = inject(Router);
  private snackBar = inject(MatSnackBar);

  // ✅ Inputs and Outputs
  @Input() purchaseOrderId?: string;
  @Output() formSubmitted = new EventEmitter<CreatePurchaseOrderDto>();
  @Output() cancelled = new EventEmitter<void>();

  // ✅ Angular Signals for reactive state
  readonly isLoading = signal<boolean>(false);
  readonly suppliers = signal<Supplier[]>([]);
  readonly selectedPurchaseOrder = signal<PurchaseOrder | null>(null);

  // ✅ RxJS for complex operations
  private destroy$ = new Subject<void>();
  
  // ✅ Reactive Forms
  purchaseOrderForm: FormGroup = this.fb.group({
    orderNumber: ['', [Validators.required, Validators.maxLength(50)]],
    totalAmount: [0, [Validators.required, Validators.min(0.01)]],
    supplierId: ['', Validators.required],
    notes: ['', Validators.maxLength(500)]
  });

  ngOnInit(): void {
    this.loadSuppliers();
    if (this.purchaseOrderId) {
      this.loadPurchaseOrder(this.purchaseOrderId);
    }
  }

  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }

  private loadSuppliers(): void {
    this.isLoading.set(true);
    
    this.purchaseOrderService.getSuppliers()
      .pipe(takeUntil(this.destroy$))
      .subscribe({
        next: (suppliers) => {
          this.suppliers.set(suppliers);
          this.isLoading.set(false);
        },
        error: (error) => {
          console.error('Error loading suppliers:', error);
          this.snackBar.open('Error loading suppliers', 'Close', { duration: 3000 });
          this.isLoading.set(false);
        }
      });
  }

  onSubmit(): void {
    if (this.purchaseOrderForm.valid) {
      const formValue = this.purchaseOrderForm.value as CreatePurchaseOrderDto;
      this.formSubmitted.emit(formValue);
    } else {
      this.markFormGroupTouched(this.purchaseOrderForm);
    }
  }

  onCancel(): void {
    this.cancelled.emit();
  }

  private markFormGroupTouched(formGroup: FormGroup): void {
    Object.keys(formGroup.controls).forEach(key => {
      const control = formGroup.get(key);
      control?.markAsTouched();
    });
  }
}
```

### Component Template
```html
<!-- ✅ CORRECTO: Template con Angular Material y structure clara -->
<mat-card class="purchase-order-form-card">
  <mat-card-header>
    <mat-card-title>
      {{ purchaseOrderId ? 'Edit Purchase Order' : 'Create Purchase Order' }}
    </mat-card-title>
  </mat-card-header>

  <mat-card-content>
    <form [formGroup]="purchaseOrderForm" 
          (ngSubmit)="onSubmit()"
          class="purchase-order-form">
      
      <!-- Order Number Field -->
      <mat-form-field appearance="outline" 
                      class="full-width">
        <mat-label>Order Number</mat-label>
        <input matInput 
               formControlName="orderNumber"
               placeholder="Enter order number"
               maxlength="50">
        <mat-hint>Maximum 50 characters</mat-hint>
        <mat-error *ngIf="purchaseOrderForm.get('orderNumber')?.hasError('required')">
          Order number is required
        </mat-error>
        <mat-error *ngIf="purchaseOrderForm.get('orderNumber')?.hasError('maxlength')">
          Order number cannot exceed 50 characters
        </mat-error>
      </mat-form-field>

      <!-- Supplier Selection -->
      <mat-form-field appearance="outline" 
                      class="full-width">
        <mat-label>Supplier</mat-label>
        <mat-select formControlName="supplierId"
                    placeholder="Select supplier">
          @for (supplier of suppliers(); track supplier.id) {
            <mat-option [value]="supplier.id">
              {{ supplier.name }}
            </mat-option>
          }
        </mat-select>
        <mat-error *ngIf="purchaseOrderForm.get('supplierId')?.hasError('required')">
          Supplier is required
        </mat-error>
      </mat-form-field>

      <!-- Total Amount -->
      <mat-form-field appearance="outline" 
                      class="full-width">
        <mat-label>Total Amount</mat-label>
        <input matInput 
               type="number"
               step="0.01"
               min="0"
               formControlName="totalAmount"
               placeholder="0.00">
        <span matTextPrefix>$&nbsp;</span>
        <mat-error *ngIf="purchaseOrderForm.get('totalAmount')?.hasError('required')">
          Total amount is required
        </mat-error>
        <mat-error *ngIf="purchaseOrderForm.get('totalAmount')?.hasError('min')">
          Total amount must be greater than zero
        </mat-error>
      </mat-form-field>

      <!-- Notes -->
      <mat-form-field appearance="outline" 
                      class="full-width">
        <mat-label>Notes</mat-label>
        <textarea matInput 
                  formControlName="notes"
                  placeholder="Additional notes (optional)"
                  rows="3"
                  maxlength="500">
        </textarea>
        <mat-hint>{{ purchaseOrderForm.get('notes')?.value?.length || 0 }}/500</mat-hint>
        <mat-error *ngIf="purchaseOrderForm.get('notes')?.hasError('maxlength')">
          Notes cannot exceed 500 characters
        </mat-error>
      </mat-form-field>

    </form>

    <!-- Loading State -->
    @if (isLoading()) {
      <div class="loading-container">
        <mat-progress-spinner diameter="40" 
                              mode="indeterminate">
        </mat-progress-spinner>
        <p>Loading...</p>
      </div>
    }
  </mat-card-content>

  <mat-card-actions align="end">
    <button mat-button 
            type="button"
            (click)="onCancel()">
      Cancel
    </button>
    <button mat-raised-button 
            color="primary"
            type="submit"
            [disabled]="!purchaseOrderForm.valid || isLoading()"
            (click)="onSubmit()">
      {{ purchaseOrderId ? 'Update' : 'Create' }}
    </button>
  </mat-card-actions>
</mat-card>
```

### Component Styles (SCSS)
```scss
// ✅ CORRECTO: SCSS with BEM-like methodology
.purchase-order-form-card {
  max-width: 800px;
  margin: 2rem auto;
  
  .purchase-order-form {
    display: flex;
    flex-direction: column;
    gap: 1.5rem;
    
    .full-width {
      width: 100%;
    }
  }
  
  .loading-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 2rem;
    
    p {
      margin-top: 1rem;
      color: mat.get-color-from-palette($foreground, secondary-text);
    }
  }
}

// ✅ Responsive design
@media (max-width: 768px) {
  .purchase-order-form-card {
    margin: 1rem;
    
    .purchase-order-form {
      gap: 1rem;
    }
  }
}

// ✅ Form validation styles
.mat-mdc-form-field {
  &.ng-invalid.ng-touched {
    .mat-mdc-text-field-wrapper {
      .mat-mdc-form-field-flex .mat-mdc-form-field-outline {
        color: mat.get-color-from-palette($warn, 500);
      }
    }
  }
}
```

## Service Implementation

### Data Service with RxJS
```typescript
// ✅ CORRECTO: Service with RxJS operators
import { Injectable, inject } from '@angular/core';
import { HttpClient, HttpParams } from '@angular/common/http';
import { Observable, BehaviorSubject, map, shareReplay, catchError, tap } from 'rxjs';
import { environment } from '../../environments/environment';

@Injectable({
  providedIn: 'root'
})
export class PurchaseOrderService {
  private http = inject(HttpClient);
  private readonly apiUrl = `${environment.apiUrl}/api/purchase-orders`;
  
  // ✅ Signal-like behavior with BehaviorSubject
  private purchaseOrdersSubject = new BehaviorSubject<PurchaseOrder[]>([]);
  public readonly purchaseOrders$ = this.purchaseOrdersSubject.asObservable();
  
  private loadingSubject = new BehaviorSubject<boolean>(false);
  public readonly loading$ = this.loadingSubject.asObservable();

  // ✅ Cache for suppliers (shared across components)
  private suppliersCache$?: Observable<Supplier[]>;

  getAllPurchaseOrders(): Observable<PurchaseOrder[]> {
    this.loadingSubject.next(true);
    
    return this.http.get<PurchaseOrder[]>(this.apiUrl)
      .pipe(
        tap(orders => this.purchaseOrdersSubject.next(orders)),
        tap(() => this.loadingSubject.next(false)),
        shareReplay(1),
        catchError(error => {
          this.loadingSubject.next(false);
          throw error;
        })
      );
  }

  getPurchaseOrderById(id: string): Observable<PurchaseOrder> {
    return this.http.get<PurchaseOrder>(`${this.apiUrl}/${id}`)
      .pipe(
        shareReplay(1)
      );
  }

  createPurchaseOrder(dto: CreatePurchaseOrderDto): Observable<PurchaseOrder> {
    return this.http.post<PurchaseOrder>(this.apiUrl, dto)
      .pipe(
        tap(newOrder => {
          const current = this.purchaseOrdersSubject.value;
          this.purchaseOrdersSubject.next([newOrder, ...current]);
        })
      );
  }

  updatePurchaseOrder(id: string, dto: UpdatePurchaseOrderDto): Observable<PurchaseOrder> {
    return this.http.put<PurchaseOrder>(`${this.apiUrl}/${id}`, dto)
      .pipe(
        tap(updatedOrder => {
          const current = this.purchaseOrdersSubject.value;
          const index = current.findIndex(o => o.id === id);
          if (index !== -1) {
            current[index] = updatedOrder;
            this.purchaseOrdersSubject.next([...current]);
          }
        })
      );
  }

  deletePurchaseOrder(id: string): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`)
      .pipe(
        tap(() => {
          const current = this.purchaseOrdersSubject.value;
          const filtered = current.filter(o => o.id !== id);
          this.purchaseOrdersSubject.next(filtered);
        })
      );
  }

  // ✅ Search with debouncing
  searchPurchaseOrders(searchTerm: string): Observable<PurchaseOrder[]> {
    const params = new HttpParams().set('search', searchTerm);
    
    return this.http.get<PurchaseOrder[]>(`${this.apiUrl}/search`, { params })
      .pipe(
        shareReplay(1)
      );
  }

  // ✅ Cached suppliers
  getSuppliers(): Observable<Supplier[]> {
    if (!this.suppliersCache$) {
      this.suppliersCache$ = this.http.get<Supplier[]>(`${environment.apiUrl}/api/suppliers`)
        .pipe(
          shareReplay(1)
        );
    }
    return this.suppliersCache$;
  }
}
```

## State Management with Signals

### State Service with Signals
```typescript
// ✅ CORRECTO: State management with Angular Signals
import { Injectable, computed, signal } from '@angular/core';

export interface PurchaseOrderState {
  orders: PurchaseOrder[];
  selectedOrder: PurchaseOrder | null;
  loading: boolean;
  error: string | null;
  filters: {
    searchTerm: string;
    supplierId: string | null;
    status: string | null;
  };
}

@Injectable({
  providedIn: 'root'
})
export class PurchaseOrderStateService {
  // ✅ Private signals for internal state
  private state = signal<PurchaseOrderState>({
    orders: [],
    selectedOrder: null,
    loading: false,
    error: null,
    filters: {
      searchTerm: '',
      supplierId: null,
      status: null
    }
  });

  // ✅ Public readonly computed signals
  readonly orders = computed(() => this.state().orders);
  readonly selectedOrder = computed(() => this.state().selectedOrder);
  readonly loading = computed(() => this.state().loading);
  readonly error = computed(() => this.state().error);
  readonly filters = computed(() => this.state().filters);

  // ✅ Filtered orders computed signal
  readonly filteredOrders = computed(() => {
    const orders = this.orders();
    const filters = this.filters();
    
    return orders.filter(order => {
      const matchesSearch = !filters.searchTerm || 
        order.orderNumber.toLowerCase().includes(filters.searchTerm.toLowerCase());
      
      const matchesSupplier = !filters.supplierId || 
        order.supplier.id === filters.supplierId;
      
      const matchesStatus = !filters.status || 
        order.status === filters.status;
      
      return matchesSearch && matchesSupplier && matchesStatus;
    });
  });

  // ✅ State update methods
  setOrders(orders: PurchaseOrder[]): void {
    this.state.update(state => ({ ...state, orders }));
  }

  addOrder(order: PurchaseOrder): void {
    this.state.update(state => ({
      ...state,
      orders: [order, ...state.orders]
    }));
  }

  updateOrder(updatedOrder: PurchaseOrder): void {
    this.state.update(state => ({
      ...state,
      orders: state.orders.map(order => 
        order.id === updatedOrder.id ? updatedOrder : order
      )
    }));
  }

  removeOrder(orderId: string): void {
    this.state.update(state => ({
      ...state,
      orders: state.orders.filter(order => order.id !== orderId)
    }));
  }

  setSelectedOrder(order: PurchaseOrder | null): void {
    this.state.update(state => ({ ...state, selectedOrder: order }));
  }

  setLoading(loading: boolean): void {
    this.state.update(state => ({ ...state, loading }));
  }

  setError(error: string | null): void {
    this.state.update(state => ({ ...state, error }));
  }

  updateFilters(filters: Partial<PurchaseOrderState['filters']>): void {
    this.state.update(state => ({
      ...state,
      filters: { ...state.filters, ...filters }
    }));
  }

  clearFilters(): void {
    this.state.update(state => ({
      ...state,
      filters: {
        searchTerm: '',
        supplierId: null,
        status: null
      }
    }));
  }
}
```

## HTTP Interceptor for Authentication

### JWT Interceptor
```typescript
// ✅ CORRECTO: HTTP Interceptor with proper error handling
import { HttpInterceptorFn, HttpRequest } from '@angular/common/http';
import { inject } from '@angular/core';
import { catchError, throwError } from 'rxjs';
import { AuthService } from '../services/auth.service';
import { Router } from '@angular/router';
import { MatSnackBar } from '@angular/material/snack-bar';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const authService = inject(AuthService);
  const router = inject(Router);
  const snackBar = inject(MatSnackBar);

  // ✅ Add JWT token to request headers
  const token = authService.getToken();
  let authReq = req;

  if (token && !isPublicRoute(req.url)) {
    authReq = req.clone({
      headers: req.headers.set('Authorization', `Bearer ${token}`)
    });
  }

  return next(authReq).pipe(
    catchError(error => {
      // ✅ Handle different HTTP error status codes
      switch (error.status) {
        case 401:
          authService.logout();
          router.navigate(['/auth/login']);
          snackBar.open('Your session has expired. Please log in again.', 'Close', {
            duration: 5000
          });
          break;
        
        case 403:
          snackBar.open('You do not have permission to access this resource.', 'Close', {
            duration: 5000
          });
          break;
        
        case 404:
          snackBar.open('The requested resource was not found.', 'Close', {
            duration: 3000
          });
          break;
        
        case 500:
          snackBar.open('A server error occurred. Please try again later.', 'Close', {
            duration: 5000
          });
          break;
        
        default:
          snackBar.open('An unexpected error occurred. Please try again.', 'Close', {
            duration: 3000
          });
      }
      
      return throwError(() => error);
    })
  );
};

function isPublicRoute(url: string): boolean {
  const publicRoutes = ['/api/auth/login', '/api/auth/register'];
  return publicRoutes.some(route => url.includes(route));
}
```

## Custom Pipe Example

### Currency Format Pipe
```typescript
// ✅ CORRECTO: Custom pipe with proper typing
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'customCurrency',
  standalone: true
})
export class CustomCurrencyPipe implements PipeTransform {
  transform(
    value: number | string | null | undefined, 
    currencyCode: string = 'USD',
    display: 'symbol' | 'code' = 'symbol'
  ): string {
    if (value === null || value === undefined || value === '') {
      return '';
    }

    const numericValue = typeof value === 'string' ? parseFloat(value) : value;
    
    if (isNaN(numericValue)) {
      return '';
    }

    return new Intl.NumberFormat('en-US', {
      style: 'currency',
      currency: currencyCode,
      currencyDisplay: display,
      minimumFractionDigits: 2,
      maximumFractionDigits: 2
    }).format(numericValue);
  }
}
```

## Smart/Container Component Pattern

### Container Component (Smart)
```typescript
// ✅ CORRECTO: Container component handling data logic
@Component({
  selector: 'app-purchase-orders-container',
  standalone: true,
  imports: [
    CommonModule,
    PurchaseOrderListComponent,
    PurchaseOrderFormComponent,
    LoadingSpinnerComponent,
    ErrorMessageComponent
  ],
  template: `
    <div class="purchase-orders-container">
      @if (stateService.loading()) {
        <app-loading-spinner></app-loading-spinner>
      } @else {
        @if (stateService.error()) {
          <app-error-message 
            [message]="stateService.error()!"
            (retry)="loadPurchaseOrders()">
          </app-error-message>
        } @else {
          <app-purchase-order-list
            [purchaseOrders]="stateService.filteredOrders()"
            [filters]="stateService.filters()"
            (orderSelected)="onOrderSelected($event)"
            (orderDeleted)="onOrderDeleted($event)"
            (filtersChanged)="onFiltersChanged($event)">
          </app-purchase-order-list>
        }
      }
    </div>
  `,
  styleUrls: ['./purchase-orders-container.component.scss']
})
export class PurchaseOrdersContainerComponent implements OnInit, OnDestroy {
  private purchaseOrderService = inject(PurchaseOrderService);
  readonly stateService = inject(PurchaseOrderStateService);
  private destroy$ = new Subject<void>();

  ngOnInit(): void {
    this.loadPurchaseOrders();
  }

  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }

  loadPurchaseOrders(): void {
    this.stateService.setLoading(true);
    this.stateService.setError(null);

    this.purchaseOrderService.getAllPurchaseOrders()
      .pipe(takeUntil(this.destroy$))
      .subscribe({
        next: (orders) => {
          this.stateService.setOrders(orders);
          this.stateService.setLoading(false);
        },
        error: (error) => {
          this.stateService.setError('Failed to load purchase orders');
          this.stateService.setLoading(false);
          console.error('Error loading purchase orders:', error);
        }
      });
  }

  onOrderSelected(order: PurchaseOrder): void {
    this.stateService.setSelectedOrder(order);
  }

  onOrderDeleted(orderId: string): void {
    this.purchaseOrderService.deletePurchaseOrder(orderId)
      .pipe(takeUntil(this.destroy$))
      .subscribe({
        next: () => {
          this.stateService.removeOrder(orderId);
        },
        error: (error) => {
          console.error('Error deleting purchase order:', error);
        }
      });
  }

  onFiltersChanged(filters: any): void {
    this.stateService.updateFilters(filters);
  }
}
```

## TypeScript Models

### Type Definitions
```typescript
// ✅ CORRECTO: TypeScript interfaces and types
export interface PurchaseOrder {
  id: string;
  orderNumber: string;
  totalAmount: number;
  createdDate: Date;
  notes?: string;
  status: PurchaseOrderStatus;
  supplier: Supplier;
  lines: PurchaseOrderLine[];
}

export interface CreatePurchaseOrderDto {
  orderNumber: string;
  totalAmount: number;
  notes?: string;
  supplierId: string;
  lines: CreatePurchaseOrderLineDto[];
}

export interface UpdatePurchaseOrderDto {
  orderNumber: string;
  totalAmount: number;
  notes?: string;
  lines: UpdatePurchaseOrderLineDto[];
}

export interface PurchaseOrderLine {
  id: string;
  productId: string;
  product: Product;
  quantity: number;
  unitPrice: number;
  lineTotal: number;
}

export interface Supplier {
  id: string;
  name: string;
  contactEmail: string;
  contactPhone?: string;
  address?: Address;
}

export enum PurchaseOrderStatus {
  Draft = 'Draft',
  Submitted = 'Submitted',
  Approved = 'Approved',
  Cancelled = 'Cancelled'
}

// ✅ Type guards
export function isPurchaseOrder(obj: any): obj is PurchaseOrder {
  return obj && 
         typeof obj.id === 'string' && 
         typeof obj.orderNumber === 'string' && 
         typeof obj.totalAmount === 'number' &&
         obj.supplier && 
         Array.isArray(obj.lines);
}

// ✅ Utility types
export type PurchaseOrderFormData = Omit<CreatePurchaseOrderDto, 'lines'>;
export type PurchaseOrderSummary = Pick<PurchaseOrder, 'id' | 'orderNumber' | 'totalAmount' | 'status'>;
```

## Environment Configuration

### Environment Files
```typescript
// ✅ environment.ts (development)
export const environment = {
  production: false,
  apiUrl: 'http://localhost:8000/gateway',
  logLevel: 'debug',
  enableDevTools: true,
  features: {
    enableExportToPdf: true,
    enableAdvancedFiltering: true
  }
};

// ✅ environment.prod.ts (production)
export const environment = {
  production: true,
  apiUrl: 'https://api.caroz.com/gateway',
  logLevel: 'error',
  enableDevTools: false,
  features: {
    enableExportToPdf: true,
    enableAdvancedFiltering: false
  }
};
```
