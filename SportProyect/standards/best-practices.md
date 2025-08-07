# Development Best Practices

## Context

Global development guidelines for Agent OS projects.

<conditional-block context-check="core-principles">
IF this Core Principles section already read in current context:
  SKIP: Re-reading this section
  NOTE: "Using Core Principles already in context"
ELSE:
  READ: The following principles

## Core Principles

### Keep It Simple
- Implement code in the fewest lines possible
- Avoid over-engineering solutions
- Choose straightforward approaches over clever ones

### Optimize for Readability
- Prioritize code clarity over micro-optimizations
- Write self-documenting code with clear variable names
- Add comments for "why" not "what"

### DRY (Don't Repeat Yourself)
- Extract repeated business logic to private methods
- Extract repeated UI markup to reusable components
- Create utility functions for common operations

### File Structure
- Keep files focused on a single responsibility
- Group related functionality together
- Use consistent naming conventions
</conditional-block>

<conditional-block context-check="dependencies" task-condition="choosing-external-library">
IF current task involves choosing an external library:
  IF Dependencies section already read in current context:
    SKIP: Re-reading this section
    NOTE: "Using Dependencies guidelines already in context"
  ELSE:
    READ: The following guidelines
ELSE:
  SKIP: Dependencies section not relevant to current task

## Dependencies

### Choose Libraries Wisely
When adding third-party dependencies:
- Select the most popular and actively maintained option
- Check the library's GitHub repository for:
  - Recent commits (within last 6 months)
  - Active issue resolution
  - Number of stars/downloads
  - Clear documentation
</conditional-block>

<conditional-block context-check="frontend-design-standards" task-condition="angular-components">
IF current task involves creating Angular components or frontend design:
  IF Frontend Design Standards section already read in current context:
    SKIP: Re-reading this section
    NOTE: "Using Frontend Design Standards already in context"
  ELSE:
    READ: The following obligatory standards
ELSE:
  SKIP: Frontend Design Standards section not relevant to current task

## Frontend Design Standards (OBLIGATORIO)

### TailwindCSS - Única Herramienta de Diseño Responsivo

**⚠️ REGLA FUNDAMENTAL: TODOS los componentes Angular DEBEN usar exclusivamente TailwindCSS**

#### Normas Obligatorias:
- ✅ **SIEMPRE** usar TailwindCSS para diseño responsivo
- ❌ **NUNCA** usar CSS personalizado para responsive design
- 📱 **OBLIGATORIO** seguir mobile-first approach
- 🎯 **REFERENCIA**: `tailwind-responsive-design-style.md`
- 📖 **DOCUMENTACIÓN COMPLETA**: `C:\Proyectos\AgentOs\TailwindcssDoc.md`

#### Patrones Obligatorios:

```html
<!-- ✅ CORRECTO: Mobile-first responsive -->
<div class="w-full md:w-1/2 lg:w-1/3 p-4 sm:p-6 md:p-8">
  <h2 class="text-xl sm:text-2xl lg:text-3xl font-bold">
    Responsive Heading
  </h2>
</div>

<!-- ❌ INCORRECTO: No responsive -->
<div class="w-1/3 p-6">
  <h2 class="text-2xl font-bold">
    Fixed Heading
  </h2>
</div>
```

#### Breakpoints Estándar (Obligatorio usar):
- **sm**: 640px (Tablets pequeñas)
- **md**: 768px (Tablets)
- **lg**: 1024px (Laptops) 
- **xl**: 1280px (Desktops)
- **2xl**: 1536px (Pantallas grandes)

#### Checklist Obligatorio por Componente:
- [ ] Mobile-first design (probado en 320px)
- [ ] Responsive en todos los breakpoints
- [ ] Texto mínimo 16px en mobile
- [ ] Botones mínimo 44px de altura en mobile
- [ ] Sin scroll horizontal en ningún breakpoint
- [ ] Estados hover/focus implementados
- [ ] Dark mode funcional automáticamente

### Validación de Cumplimiento

**No se aceptarán componentes que no cumplan estas normas.**

Cada componente Angular debe:
1. Usar exclusivamente clases de Tailwind
2. Implementar diseño mobile-first
3. Ser funcional en todos los breakpoints
4. Pasar el checklist de validación responsiva

</conditional-block>
