# HTML Style Guide para SportProyect

## Estructura y Semántica HTML5

### Estructura Base de Documento

```html
<!DOCTYPE html>
<html lang="es" class="h-full">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="SportProyect - Sistema de gestión deportiva">
  <meta name="keywords" content="deportes, gestión, equipos, partidos">
  <meta name="author" content="SportProyect Team">
  
  <!-- Open Graph Meta Tags -->
  <meta property="og:title" content="SportProyect">
  <meta property="og:description" content="Sistema de gestión deportiva profesional">
  <meta property="og:image" content="/assets/og-image.jpg">
  <meta property="og:url" content="https://sportproyect.com">
  <meta property="og:type" content="website">
  
  <!-- Twitter Card Meta Tags -->
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="SportProyect">
  <meta name="twitter:description" content="Sistema de gestión deportiva profesional">
  <meta name="twitter:image" content="/assets/twitter-card.jpg">
  
  <!-- Favicon -->
  <link rel="icon" type="image/svg+xml" href="/favicon.svg">
  <link rel="alternate icon" href="/favicon.ico">
  <link rel="apple-touch-icon" href="/apple-touch-icon.png">
  
  <!-- Preconnect to external domains -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://your-project.supabase.co">
  
  <title>SportProyect - Inicio</title>
</head>
<body class="h-full bg-gray-50 dark:bg-gray-900">
  <app-root></app-root>
</body>
</html>
```

## Elementos Semánticos

### Layout Principal

```html
<!-- Header con navegación -->
<header class="bg-white dark:bg-gray-800 shadow-sm">
  <nav class="container mx-auto px-4" role="navigation" aria-label="Navegación principal">
    <div class="flex items-center justify-between h-16">
      <!-- Logo -->
      <a href="/" class="flex items-center" aria-label="Ir al inicio">
        <img src="/logo.svg" alt="SportProyect Logo" class="h-8 w-auto">
        <span class="ml-2 text-xl font-semibold">SportProyect</span>
      </a>
      
      <!-- Navigation -->
      <ul class="hidden md:flex items-center space-x-8">
        <li>
          <a href="/teams" class="text-gray-700 hover:text-primary-600 dark:text-gray-300 dark:hover:text-primary-400">
            Equipos
          </a>
        </li>
        <li>
          <a href="/matches" class="text-gray-700 hover:text-primary-600 dark:text-gray-300 dark:hover:text-primary-400">
            Partidos
          </a>
        </li>
        <li>
          <a href="/players" class="text-gray-700 hover:text-primary-600 dark:text-gray-300 dark:hover:text-primary-400">
            Jugadores
          </a>
        </li>
      </ul>
      
      <!-- Mobile menu button -->
      <button 
        type="button"
        class="md:hidden p-2 rounded-md text-gray-700 dark:text-gray-300"
        aria-label="Abrir menú"
        aria-expanded="false"
        aria-controls="mobile-menu"
      >
        <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
        </svg>
      </button>
    </div>
  </nav>
</header>

<!-- Main content area -->
<main id="main-content" class="flex-1">
  <section class="py-12">
    <div class="container mx-auto px-4">
      <h1 class="text-3xl font-bold text-gray-900 dark:text-white">
        Título de la Página
      </h1>
      <!-- Content -->
    </div>
  </section>
</main>

<!-- Footer -->
<footer class="bg-gray-800 text-white">
  <div class="container mx-auto px-4 py-8">
    <div class="grid grid-cols-1 md:grid-cols-4 gap-8">
      <!-- Company info -->
      <div>
        <h3 class="text-lg font-semibold mb-4">SportProyect</h3>
        <p class="text-gray-400">
          Sistema profesional de gestión deportiva
        </p>
      </div>
      
      <!-- Links columns -->
      <nav aria-label="Enlaces del sitio">
        <h3 class="text-lg font-semibold mb-4">Producto</h3>
        <ul class="space-y-2">
          <li><a href="/features" class="text-gray-400 hover:text-white">Características</a></li>
          <li><a href="/pricing" class="text-gray-400 hover:text-white">Precios</a></li>
        </ul>
      </nav>
    </div>
    
    <!-- Copyright -->
    <div class="mt-8 pt-8 border-t border-gray-700 text-center text-gray-400">
      <p>&copy; 2024 SportProyect. Todos los derechos reservados.</p>
    </div>
  </div>
</footer>
```

### Artículos y Contenido

```html
<!-- Article structure -->
<article class="bg-white dark:bg-gray-800 rounded-lg shadow-md overflow-hidden">
  <header class="p-6 border-b border-gray-200 dark:border-gray-700">
    <h2 class="text-2xl font-bold text-gray-900 dark:text-white">
      Título del Artículo
    </h2>
    <div class="mt-2 flex items-center text-sm text-gray-500 dark:text-gray-400">
      <time datetime="2024-01-15">15 de enero, 2024</time>
      <span class="mx-2">•</span>
      <span>Por <a href="/author/juan" class="hover:text-primary-600">Juan Pérez</a></span>
    </div>
  </header>
  
  <div class="prose prose-lg dark:prose-invert max-w-none p-6">
    <p>Contenido del artículo...</p>
  </div>
  
  <footer class="p-6 bg-gray-50 dark:bg-gray-900">
    <div class="flex items-center justify-between">
      <div class="flex items-center space-x-4">
        <button type="button" class="flex items-center text-gray-500 hover:text-primary-600">
          <svg class="w-5 h-5 mr-1" fill="currentColor" viewBox="0 0 20 20">
            <!-- Heart icon -->
          </svg>
          <span>Me gusta</span>
        </button>
      </div>
    </div>
  </footer>
</article>
```

### Formularios Accesibles

```html
<!-- Form with proper labels and structure -->
<form class="space-y-6" novalidate>
  <!-- Text input -->
  <div class="form-group">
    <label for="team-name" class="form-label">
      Nombre del Equipo
      <span class="text-red-500" aria-label="campo requerido">*</span>
    </label>
    <input 
      type="text" 
      id="team-name" 
      name="teamName"
      class="form-input"
      required
      aria-required="true"
      aria-describedby="team-name-error"
      placeholder="Ej: Real Madrid"
    >
    <p id="team-name-error" class="form-error hidden" role="alert">
      El nombre del equipo es requerido
    </p>
  </div>
  
  <!-- Select dropdown -->
  <div class="form-group">
    <label for="division" class="form-label">División</label>
    <select 
      id="division" 
      name="division"
      class="form-input"
      aria-describedby="division-help"
    >
      <option value="">Selecciona una división</option>
      <option value="primera">Primera División</option>
      <option value="segunda">Segunda División</option>
      <option value="tercera">Tercera División</option>
    </select>
    <p id="division-help" class="mt-1 text-sm text-gray-500">
      Selecciona la división en la que compite el equipo
    </p>
  </div>
  
  <!-- Radio buttons -->
  <fieldset>
    <legend class="form-label">Tipo de Equipo</legend>
    <div class="space-y-2 mt-2">
      <div class="flex items-center">
        <input 
          type="radio" 
          id="professional" 
          name="teamType" 
          value="professional"
          class="h-4 w-4 text-primary-600 focus:ring-primary-500"
        >
        <label for="professional" class="ml-2 text-sm text-gray-700 dark:text-gray-300">
          Profesional
        </label>
      </div>
      <div class="flex items-center">
        <input 
          type="radio" 
          id="amateur" 
          name="teamType" 
          value="amateur"
          class="h-4 w-4 text-primary-600 focus:ring-primary-500"
        >
        <label for="amateur" class="ml-2 text-sm text-gray-700 dark:text-gray-300">
          Amateur
        </label>
      </div>
    </div>
  </fieldset>
  
  <!-- Checkbox -->
  <div class="flex items-start">
    <input 
      type="checkbox" 
      id="terms" 
      name="acceptTerms"
      class="h-4 w-4 mt-1 text-primary-600 focus:ring-primary-500"
      aria-describedby="terms-description"
    >
    <div class="ml-2">
      <label for="terms" class="text-sm text-gray-700 dark:text-gray-300">
        Acepto los términos y condiciones
      </label>
      <p id="terms-description" class="text-xs text-gray-500 mt-1">
        Al marcar esta casilla, aceptas nuestros 
        <a href="/terms" class="text-primary-600 hover:underline">términos de servicio</a>
      </p>
    </div>
  </div>
  
  <!-- File upload -->
  <div class="form-group">
    <label for="team-logo" class="form-label">Logo del Equipo</label>
    <input 
      type="file" 
      id="team-logo" 
      name="teamLogo"
      accept="image/png,image/jpeg,image/webp"
      class="block w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-md file:border-0 file:text-sm file:font-semibold file:bg-primary-50 file:text-primary-700 hover:file:bg-primary-100"
      aria-describedby="logo-requirements"
    >
    <p id="logo-requirements" class="mt-1 text-sm text-gray-500">
      PNG, JPG o WebP. Máximo 5MB.
    </p>
  </div>
  
  <!-- Submit button -->
  <div class="flex items-center justify-between">
    <button 
      type="submit" 
      class="btn btn-primary"
      aria-busy="false"
    >
      <span class="button-text">Guardar Equipo</span>
      <span class="button-loader hidden">
        <svg class="animate-spin h-5 w-5" viewBox="0 0 24 24">
          <!-- Spinner -->
        </svg>
      </span>
    </button>
    
    <button type="button" class="btn btn-secondary">
      Cancelar
    </button>
  </div>
</form>
```

### Tablas de Datos

```html
<!-- Responsive data table -->
<div class="overflow-x-auto">
  <table class="table" role="table">
    <caption class="sr-only">Lista de jugadores del equipo</caption>
    <thead>
      <tr>
        <th scope="col" class="table-cell text-left">
          <button type="button" class="flex items-center font-medium text-gray-900 dark:text-white">
            Nombre
            <svg class="ml-1 h-4 w-4" fill="currentColor" viewBox="0 0 20 20">
              <!-- Sort icon -->
            </svg>
          </button>
        </th>
        <th scope="col" class="table-cell text-center">Posición</th>
        <th scope="col" class="table-cell text-center">Número</th>
        <th scope="col" class="table-cell text-center">Edad</th>
        <th scope="col" class="table-cell text-right">
          <span class="sr-only">Acciones</span>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr class="table-row">
        <td class="table-cell">
          <div class="flex items-center">
            <img 
              src="/player-1.jpg" 
              alt="Foto de Juan García"
              class="h-10 w-10 rounded-full object-cover"
              loading="lazy"
            >
            <div class="ml-4">
              <div class="font-medium text-gray-900 dark:text-white">
                Juan García
              </div>
              <div class="text-sm text-gray-500">
                Capitán
              </div>
            </div>
          </div>
        </td>
        <td class="table-cell text-center">
          <span class="badge badge-primary">Delantero</span>
        </td>
        <td class="table-cell text-center font-mono">10</td>
        <td class="table-cell text-center">28</td>
        <td class="table-cell text-right">
          <button 
            type="button" 
            class="text-primary-600 hover:text-primary-900"
            aria-label="Ver detalles de Juan García"
          >
            Ver detalles
          </button>
        </td>
      </tr>
    </tbody>
  </table>
</div>
```

### Listas y Navegación

```html
<!-- Navigation list -->
<nav aria-label="Navegación principal">
  <ul class="space-y-1">
    <li>
      <a 
        href="/dashboard" 
        class="flex items-center px-4 py-2 text-sm font-medium rounded-md bg-primary-100 text-primary-700"
        aria-current="page"
      >
        <svg class="mr-3 h-5 w-5" fill="currentColor" viewBox="0 0 20 20">
          <!-- Icon -->
        </svg>
        Dashboard
      </a>
    </li>
    <li>
      <a 
        href="/teams" 
        class="flex items-center px-4 py-2 text-sm font-medium rounded-md text-gray-600 hover:bg-gray-50"
      >
        <svg class="mr-3 h-5 w-5" fill="currentColor" viewBox="0 0 20 20">
          <!-- Icon -->
        </svg>
        Equipos
      </a>
    </li>
  </ul>
</nav>

<!-- Breadcrumb navigation -->
<nav aria-label="Breadcrumb" class="mb-4">
  <ol class="flex items-center space-x-2 text-sm">
    <li>
      <a href="/" class="text-gray-500 hover:text-gray-700">
        Inicio
      </a>
    </li>
    <li>
      <span class="text-gray-400">/</span>
    </li>
    <li>
      <a href="/teams" class="text-gray-500 hover:text-gray-700">
        Equipos
      </a>
    </li>
    <li>
      <span class="text-gray-400">/</span>
    </li>
    <li>
      <span class="text-gray-900 dark:text-white" aria-current="page">
        Real Madrid
      </span>
    </li>
  </ol>
</nav>

<!-- Pagination -->
<nav aria-label="Paginación de resultados" class="flex items-center justify-between">
  <div class="flex-1 flex justify-between sm:hidden">
    <a href="?page=1" class="btn btn-secondary">Anterior</a>
    <a href="?page=3" class="btn btn-secondary">Siguiente</a>
  </div>
  <div class="hidden sm:flex-1 sm:flex sm:items-center sm:justify-between">
    <div>
      <p class="text-sm text-gray-700 dark:text-gray-300">
        Mostrando <span class="font-medium">21</span> a <span class="font-medium">30</span> de{' '}
        <span class="font-medium">97</span> resultados
      </p>
    </div>
    <div>
      <nav class="relative z-0 inline-flex rounded-md shadow-sm -space-x-px" aria-label="Pagination">
        <a 
          href="?page=1" 
          class="relative inline-flex items-center px-2 py-2 rounded-l-md border border-gray-300 bg-white text-sm font-medium text-gray-500 hover:bg-gray-50"
          aria-label="Página anterior"
        >
          <svg class="h-5 w-5" fill="currentColor" viewBox="0 0 20 20">
            <!-- Previous icon -->
          </svg>
        </a>
        <a href="?page=1" class="relative inline-flex items-center px-4 py-2 border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50">
          1
        </a>
        <span aria-current="page" class="relative inline-flex items-center px-4 py-2 border border-primary-500 bg-primary-50 text-sm font-medium text-primary-600">
          2
        </span>
        <a href="?page=3" class="relative inline-flex items-center px-4 py-2 border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50">
          3
        </a>
      </nav>
    </div>
  </div>
</nav>
```

### Modales y Diálogos

```html
<!-- Modal dialog -->
<div 
  class="fixed inset-0 z-50 overflow-y-auto" 
  aria-labelledby="modal-title" 
  role="dialog" 
  aria-modal="true"
>
  <!-- Background overlay -->
  <div class="fixed inset-0 bg-black bg-opacity-50 transition-opacity" aria-hidden="true"></div>
  
  <!-- Modal panel -->
  <div class="flex min-h-full items-center justify-center p-4">
    <div class="relative bg-white dark:bg-gray-800 rounded-lg shadow-xl max-w-md w-full">
      <!-- Modal header -->
      <div class="px-6 py-4 border-b border-gray-200 dark:border-gray-700">
        <h3 id="modal-title" class="text-lg font-semibold text-gray-900 dark:text-white">
          Confirmar acción
        </h3>
        <button 
          type="button" 
          class="absolute top-4 right-4 text-gray-400 hover:text-gray-500"
          aria-label="Cerrar modal"
        >
          <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>
      </div>
      
      <!-- Modal body -->
      <div class="px-6 py-4">
        <p class="text-gray-600 dark:text-gray-300">
          ¿Estás seguro de que deseas eliminar este equipo? Esta acción no se puede deshacer.
        </p>
      </div>
      
      <!-- Modal footer -->
      <div class="px-6 py-4 bg-gray-50 dark:bg-gray-900 rounded-b-lg flex justify-end space-x-2">
        <button type="button" class="btn btn-secondary">
          Cancelar
        </button>
        <button type="button" class="btn btn-danger">
          Eliminar
        </button>
      </div>
    </div>
  </div>
</div>
```

## Mejores Prácticas HTML

### 1. Semántica Correcta

```html
<!-- ✅ Correcto -->
<nav>
  <ul>
    <li><a href="/home">Inicio</a></li>
  </ul>
</nav>

<!-- ❌ Incorrecto -->
<div class="navigation">
  <div class="nav-list">
    <div class="nav-item"><span onclick="navigate('/home')">Inicio</span></div>
  </div>
</div>
```

### 2. Accesibilidad (a11y)

```html
<!-- Imágenes con alt text descriptivo -->
<img 
  src="team-celebration.jpg" 
  alt="El equipo celebrando la victoria en el campeonato 2024"
  loading="lazy"
>

<!-- Skip links -->
<a href="#main-content" class="sr-only focus:not-sr-only">
  Saltar al contenido principal
</a>

<!-- ARIA labels cuando sea necesario -->
<button 
  type="button"
  aria-label="Menú de navegación"
  aria-expanded="false"
  aria-controls="mobile-menu"
>
  <svg><!-- Menu icon --></svg>
</button>

<!-- Focus indicators -->
<a href="#" class="focus:outline-none focus:ring-2 focus:ring-primary-500">
  Enlace accesible
</a>
```

### 3. Formularios

```html
<!-- Siempre asocia labels con inputs -->
<label for="email">Correo electrónico</label>
<input type="email" id="email" name="email" required>

<!-- Agrupa campos relacionados -->
<fieldset>
  <legend>Información de contacto</legend>
  <!-- Fields -->
</fieldset>

<!-- Mensajes de error claros -->
<input 
  type="text" 
  aria-invalid="true" 
  aria-describedby="error-message"
>
<p id="error-message" role="alert" class="text-red-600">
  Este campo es requerido
</p>
```

### 4. Performance

```html
<!-- Lazy loading para imágenes -->
<img src="player.jpg" loading="lazy" alt="Jugador">

<!-- Preload recursos críticos -->
<link rel="preload" href="/fonts/inter.woff2" as="font" type="font/woff2" crossorigin>

<!-- DNS prefetch para dominios externos -->
<link rel="dns-prefetch" href="https://your-project.supabase.co">

<!-- Async/defer para scripts -->
<script src="analytics.js" async></script>
<script src="app.js" defer></script>
```

### 5. SEO y Meta Tags

```html
<!-- Título único por página -->
<title>Equipos - SportProyect</title>

<!-- Meta descripción única -->
<meta name="description" content="Gestiona todos tus equipos deportivos en un solo lugar con SportProyect">

<!-- Canonical URL -->
<link rel="canonical" href="https://sportproyect.com/teams">

<!-- Structured data -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "SportsTeam",
  "name": "Real Madrid",
  "sport": "Football",
  "url": "https://sportproyect.com/teams/real-madrid"
}
</script>
```

## Convenciones de Atributos

### Orden de Atributos

```html
<!-- Orden recomendado para mejor legibilidad -->
<input
  type="text"
  id="player-name"
  name="playerName"
  class="form-input"
  placeholder="Nombre del jugador"
  value=""
  required
  aria-required="true"
  aria-describedby="player-name-help"
  data-testid="player-name-input"
>
```

1. `type` (para inputs)
2. `id`
3. `name`
4. `class`
5. `placeholder`, `value`, otros atributos de contenido
6. Atributos booleanos (`required`, `disabled`, etc.)
7. ARIA attributes
8. Data attributes
9. Event handlers (evitar inline cuando sea posible)

### Convenciones de ID y Clases

```html
<!-- IDs: kebab-case, únicos, descriptivos -->
<div id="match-schedule-modal">

<!-- Clases: siguiendo convenciones de Tailwind -->
<div class="flex items-center space-x-4">

<!-- Data attributes para JavaScript -->
<button data-action="delete-team" data-team-id="123">

<!-- Test IDs para testing -->
<form data-testid="team-creation-form">
```

## Snippets Reutilizables

### Loading States

```html
<!-- Skeleton loader -->
<div class="animate-pulse">
  <div class="h-4 bg-gray-300 rounded w-3/4 mb-4"></div>
  <div class="h-4 bg-gray-300 rounded w-1/2"></div>
</div>

<!-- Spinner -->
<div class="flex justify-center items-center p-4">
  <svg class="animate-spin h-8 w-8 text-primary-600" fill="none" viewBox="0 0 24 24">
    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"></path>
  </svg>
</div>
```

### Empty States

```html
<div class="text-center py-12">
  <svg class="mx-auto h-12 w-12 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
    <!-- Empty icon -->
  </svg>
  <h3 class="mt-2 text-sm font-medium text-gray-900">No hay equipos</h3>
  <p class="mt-1 text-sm text-gray-500">Comienza creando un nuevo equipo.</p>
  <div class="mt-6">
    <button type="button" class="btn btn-primary">
      <svg class="mr-2 h-5 w-5" fill="currentColor" viewBox="0 0 20 20">
        <!-- Plus icon -->
      </svg>
      Nuevo Equipo
    </button>
  </div>
</div>
```

## Validación HTML

1. **Siempre validar**: Usa el validador W3C
2. **Cerrar todos los tags**: Incluso los auto-cerrados en XHTML
3. **Atributos únicos**: No repetir IDs
4. **Anidación correcta**: Respetar las reglas de anidación HTML
5. **Caracteres especiales**: Usar entidades HTML cuando sea necesario