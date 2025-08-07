# Code Style Guide

## Context

Global code style rules for Agent OS projects.

<conditional-block context-check="general-formatting">
IF this General Formatting section already read in current context:
  SKIP: Re-reading this section
  NOTE: "Using General Formatting rules already in context"
ELSE:
  READ: The following formatting rules

## General Formatting

### Indentation
- Use 2 spaces for indentation (never tabs)
- Maintain consistent indentation throughout files
- Align nested structures for readability

### Naming Conventions
- **Methods and Variables**: Use snake_case (e.g., `user_profile`, `calculate_total`)
- **Classes and Modules**: Use PascalCase (e.g., `UserProfile`, `PaymentProcessor`)
- **Constants**: Use UPPER_SNAKE_CASE (e.g., `MAX_RETRY_COUNT`)

### String Formatting
- Use single quotes for strings: `'Hello World'`
- Use double quotes only when interpolation is needed
- Use template literals for multi-line strings or complex interpolation

### Code Comments
- Add brief comments above non-obvious business logic
- Document complex algorithms or calculations
- Explain the "why" behind implementation choices
- Never remove existing comments unless removing the associated code
- Update comments when modifying code to maintain accuracy
- Keep comments concise and relevant
</conditional-block>

<conditional-block task-condition="html" context-check="html-style">
IF current task involves writing or updating HTML:
  IF html-style.md already in context:
    SKIP: Re-reading this file
    NOTE: "Using HTML style guide already in context"
  ELSE:
    <context_fetcher_strategy>
      READ: @~/.agent-os/standards/code-style/html-style.md
    </context_fetcher_strategy>
ELSE:
  SKIP: HTML style guide not relevant to current task
</conditional-block>

<conditional-block task-condition="css-tailwind" context-check="css-style">
IF current task involves writing CSS or TailwindCSS:
  IF css-tailwind-style.md already in context:
    SKIP: Re-reading this file
    NOTE: "Using CSS/Tailwind style guide already in context"
  ELSE:
    <context_fetcher_strategy>
      READ: @~/.agent-os/standards/code-style/css-tailwind-style.md
    </context_fetcher_strategy>
ELSE:
  SKIP: CSS/Tailwind style guide not relevant to current task
</conditional-block>

<conditional-block task-condition="javascript" context-check="javascript-style">
IF current task involves writing or updating JavaScript:
  IF javascript-style.md already in context:
    SKIP: Re-reading this file
    NOTE: "Using JavaScript style guide already in context"
  ELSE:
    <context_fetcher_strategy>
      IF current agent is Claude Code AND context-fetcher agent exists:
        USE: @agent:context-fetcher
        REQUEST: "Get JavaScript style rules from code-style/javascript-style.md"
        PROCESS: Returned style rules
      ELSE:
        read: @~/.agent-os/standards/code-style/javascript-style.md
    </context_fetcher_strategy>
ELSE:
  SKIP: JavaScript style guide not relevant to current task
</conditional-block>

<conditional-block task-condition="net8" context-check="net8-style">
IF current task involves writing or updating .NET 8 code:
  IF net8-style.md already in context:
    SKIP: Re-reading this file
    NOTE: "Using .NET 8 style guide already in context"
  ELSE:
    <context_fetcher_strategy>
      READ: @~/.agent-os/standards/code-style/net8-style.md
    </context_fetcher_strategy>
ELSE:
  SKIP: .NET 8 style guide not relevant to current task
</conditional-block>

<conditional-block task-condition="angular" context-check="angular-style">
IF current task involves writing or updating Angular components:
  IF angular-style.md AND typescript-style.md already in context:
    SKIP: Re-reading these files
    NOTE: "Using Angular and TypeScript style guides already in context"
  ELSE:
    <context_fetcher_strategy>
      READ: @~/.agent-os/standards/code-style/angular-style.md
      READ: @~/.agent-os/standards/code-style/typescript-style.md
    </context_fetcher_strategy>
  
  // OBLIGATORIO: Si es un componente Angular, cargar guía de TailwindCSS
  IF tailwind-responsive-design-style.md NOT already in context:
    <context_fetcher_strategy>
      READ: @~/.agent-os/standards/code-style/tailwind-responsive-design-style.md
      NOTE: "OBLIGATORIO: Todos los componentes Angular DEBEN usar TailwindCSS para diseño responsivo"
    </context_fetcher_strategy>
ELSE:
  SKIP: Angular style guide not relevant to current task
</conditional-block>

<conditional-block task-condition="typescript" context-check="typescript-style">
IF current task involves writing TypeScript (non-Angular):
  IF typescript-style.md already in context:
    SKIP: Re-reading this file
    NOTE: "Using TypeScript style guide already in context"
  ELSE:
    <context_fetcher_strategy>
      READ: @~/.agent-os/standards/code-style/typescript-style.md
    </context_fetcher_strategy>
ELSE:
  SKIP: TypeScript style guide not relevant to current task
</conditional-block>

<conditional-block task-condition="supabase" context-check="supabase-style">
IF current task involves Supabase integration:
  IF supabase-style.md already in context:
    SKIP: Re-reading this file
    NOTE: "Using Supabase style guide already in context"
  ELSE:
    <context_fetcher_strategy>
      READ: @~/.agent-os/standards/code-style/supabase-style.md
    </context_fetcher_strategy>
ELSE:
  SKIP: Supabase style guide not relevant to current task
</conditional-block>

<conditional-block task-condition="theming" context-check="theme-system-style">
IF current task involves theming, dark/light mode, or theme toggle implementation:
  IF theme-system-style.md already in context:
    SKIP: Re-reading this file
    NOTE: "Using Theme System style guide already in context"
  ELSE:
    <context_fetcher_strategy>
      READ: @~/.agent-os/standards/code-style/theme-system-style.md
    </context_fetcher_strategy>
ELSE:
  SKIP: Theme System style guide not relevant to current task
</conditional-block>
