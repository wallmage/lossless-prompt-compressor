# Lossless Prompt Compressor

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

Comprime documentos de instrucciones para AI, archivos de reglas, documentos de planificación y especificaciones de producto. Sin perder ningún hecho.

Esto es compresión, no resumen. Los hechos relevantes para la máquina se mantienen; el formato superfluo, las redundancias y el contenido solo para humanos se eliminan. Opcionalmente, reescribe todo en taquigrafía ultradensa exclusiva para AI.

## Modos

- **Strict Lossless** (por defecto): Limpieza mecánica (Pass 1) + ediciones aprobadas que preservan hechos (Pass 2). Cada hecho sobrevive. Sigue siendo legible para humanos.
- **AI-Lossless**: Añade el Pass 3. Elimina tutoriales, coaching, calendarios — todo lo que solo un humano leería.
- **AI-Only**: Añade el Pass 4. Reescritura completa en estilo telegráfico para máxima densidad.

## Flujo de trabajo

| Pass | Tipo | Aprobación | Reducción típica |
|------|------|------------|-----------------|
| 1 | Limpieza mecánica | Automática | 10-25% |
| 2 | Compresión con preservación de hechos | Por elemento | +5-15% |
| 3 | Eliminación de contenido humano | Por elemento | +15-25% |
| 4 | Reescritura telegráfica | Opt-in explícito | +25-40% |

El Pass 1 quita el embalaje. El Pass 2 sella al vacío. El Pass 3 descarta el manual de instrucciones que solo un humano necesitaría. El Pass 4 convierte todo a taquigrafía.

## Diseño

- Strict Lossless por defecto: compresión, no resumen
- Números de sección seguros: los anclajes de referencia cruzada nunca se renumeran
- Control por aprobación: toda edición no mecánica requiere confirmación del usuario
- 23 técnicas numeradas con ejemplos antes/después en `references/techniques.md`
- 12 casos límite que cubren desde prompts con mucho código hasta documentos multilingües

## Estructura

```
lossless-prompt-compressor/
├── SKILL.md              — Manual de operación (~350 líneas)
└── references/
    └── techniques.md     — Catálogo completo de técnicas con ejemplos
```

## Autor

[Wallny](https://github.com/wallmage)
