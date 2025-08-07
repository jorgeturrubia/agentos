# Agent OS - YAML Format

## Descripción

Este proyecto es una migración del sistema Agent OS al formato YAML estructurado, proporcionando una forma más robusta, predecible y verificable para definir flujos de trabajo para agentes basados en LLM.

## Estructura del Proyecto

```
StandardAgentYaml/
├── claude-code/
│   └── agents/         # Subagentes especializados en formato YAML
├── commands/           # Comandos principales disponibles
├── instructions/
│   ├── core/           # Instrucciones detalladas en formato YAML
│   └── meta/           # Metainstrucciones y configuración
└── standards/          # Estándares de código y desarrollo
```

## Migración de Formatos

Este proyecto migra desde un formato basado en markdown con scripts bash incrustados hacia un formato YAML estructurado, que proporciona:

1. **Estructura de Pasos Explícita**: Define cada paso con identificadores únicos, tipos y validaciones.
2. **Sistema de Validación Mejorado**: Verificaciones pre y post ejecución para asegurar la calidad.
3. **Invocación Explícita de Subagentes**: Define claramente cómo y cuándo invocar otros agentes.
4. **Checkpoints Obligatorios**: Establece puntos de verificación para confirmar el progreso.
5. **Logging y Debug**: Sistema detallado para registrar cada paso y facilitar la solución de problemas.
6. **Flujo Visual**: Diagramas Mermaid para visualizar el proceso completo.

## Beneficios del Formato YAML

- **Estructura Clara**: Define explícitamente pasos, validaciones y flujos de trabajo.
- **Robustez**: Reduce la ambigüedad y aumenta la previsibilidad.
- **Verificación**: Facilita la validación automática del flujo de trabajo.
- **Mantenimiento**: Facilita la actualización y extensión del sistema.
- **Visualización**: Proporciona representación visual de los flujos de trabajo.

## Uso

Para utilizar este sistema, los agentes deben seguir estos pasos:

1. Leer el comando específico en la carpeta `commands/`
2. Consultar las instrucciones detalladas en formato YAML en `instructions/core/`
3. Seguir el flujo de trabajo definido, respetando los checkpoints y validaciones
4. Utilizar los subagentes según se especifica en la sección de invocación

## Ejemplo de Estructura YAML

```yaml
steps:
  - id: gather_input
    name: "Recopilar Información"
    type: input
    required_fields:
      - main_idea: 
          type: string
          min_length: 10
          prompt: "¿Cuál es la idea principal del producto?"
    validation:
      on_missing: BLOCK
      message: "Falta información requerida: {missing_fields}"
```
