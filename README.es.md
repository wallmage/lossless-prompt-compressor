# Lossless Prompt Compressor

[![GitHub stars](https://img.shields.io/github/stars/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/network/members)
[![GitHub watchers](https://img.shields.io/github/watchers/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/watchers)
[![GitHub last commit](https://img.shields.io/github/last-commit/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/commits/main)
[![GitHub repo size](https://img.shields.io/github/repo-size/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Top language](https://img.shields.io/github/languages/top/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Compression](https://img.shields.io/badge/compression-lossless-111927?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Focus](https://img.shields.io/badge/focus-prompt%20compaction-0f766e?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Works with](https://img.shields.io/badge/works%20with-LLM%20%7C%20agent%20%7C%20chat-4f46e5?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Output](https://img.shields.io/badge/output-smaller%20context-b45309?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Guarantee](https://img.shields.io/badge/guarantee-no%20facts%20lost-2563eb?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | Español | [Italiano](README.it.md) | [Português](README.pt-BR.md)

## Por que existe esto

Cada token en tu system prompt es un impuesto que pagas en cada llamada a la API. Un archivo de instrucciones de 35.000 palabras significa unos 48.000 tokens de contexto consumidos antes de que el modelo lea un solo mensaje del usuario. Multiplica eso por miles de peticiones al dia y estas quemando dinero en formato, redundancia y prosa que solo un humano necesitaria leer.

Construi esto para solucionar ese problema. Lossless Prompt Compressor toma documentos de instrucciones para AI -- system prompts, archivos CLAUDE.md, reglas de agentes, especificaciones de producto, planes maestros -- y los comprime a fondo. No resume. Comprime. Cada hecho, cada nombre de campo, cada restriccion, cada referencia cruzada sobrevive intacta. Lo que se corta es el empaquetado: formato Markdown que el modelo nunca necesito, declaraciones duplicadas, andamiaje de tutoriales, relleno motivacional y tejido conectivo gramatical que cuesta tokens sin transportar informacion.

En una especificacion de producto tipica, obtienes un 50-65% de reduccion a lo largo de los cuatro pases. Eso es dinero real de vuelta en tu bolsillo en cada llamada a la API, y ventana de contexto real liberada para la conversacion que realmente importa.

Este compresor tambien es el motor del paso de compresion dentro de [vibecheck](https://github.com/wallmage/vibecheck).

## Que significa realmente "lossless"

Esto no es un resumen. Un resumen decide que es importante y descarta el resto. Eso es peligroso para archivos de instrucciones -- falta una restriccion y el modelo alucina una funcionalidad que excluiste explicitamente.

Lossless significa: si el documento original dice "limitar los subsidios del tier gratuito al 3% de los ingresos mensuales", la version comprimida dice exactamente lo mismo. Ningun hecho se elimina, se reformula de forma ambigua ni se fusiona silenciosamente. Los numeros de seccion permanecen fijos para que las referencias cruzadas ("ver S8.7.3") nunca se rompan. La AI produce decisiones de codigo y arquitectura identicas a partir de la version comprimida y de la original.

La garantia se refuerza mediante un portal de aprobacion -- cada edicion no mecanica se propone con un diff antes/despues y solo se aplica tras tu aprobacion.

## 23 tecnicas en 4 pases

El compresor funciona en cuatro pases progresivos. Cada uno profundiza mas. Tu decides donde parar.

### Pass 1 -- Limpieza mecanica (automatica, 10-25% de reduccion)

Sin juicios de valor. Este paso elimina formato que los LLMs parsean de forma identica este o no presente:

- **Eliminacion de Markdown**: encabezados `##`, `**negrita**`, viñetas `- `, pipes de tabla `| |`, vallas de codigo ` ``` `, citas `> `, lineas `---` -- todo eliminado. El texto y la estructura quedan; la decoracion se va.
- **Conversion de formato**: las tablas se convierten en filas separadas por punto y coma. Las listas con viñetas se convierten en elementos inline separados por punto y coma. La sintaxis de enlace `[texto](url)` se reduce a la URL.
- **Escaneo de verificacion**: tras la eliminacion, un escaneo completo detecta residuos de Markdown. Los colores hexadecimales (`#FF0000`), los globs de regex (`*.txt`), los pipes de shell y el frontmatter YAML se reconocen como contenido y se preservan.

Tecnicas 1-2 del catalogo. Seguro para ejecutar en cualquier documento. Ahorro tipico: 10-25% de reduccion de palabras.

### Pass 2 -- Compresion que preserva hechos (requiere aprobacion, +5-15%)

Aqui es donde se pone creativo. Cada edicion propuesta se te muestra con el ahorro estimado y solo se aplica tras tu aprobacion:

- **Deduplicacion por referencias cruzadas** (Tecnica 3): hechos declarados en tres lugares se declaran una vez; los demas se reemplazan por un puntero como "segun S6".
- **Compresion de bloques de codigo** (Tecnica 4): las definiciones de struct/class se convierten en descripciones inline compactas preservando todos los nombres de campo, tipos y enums. Excepcion: Python/YAML/Makefile se mantienen intactos porque el espacio en blanco es semantico.
- **Compresion de config JSON** (Tecnica 5): los ejemplos JSON completos se convierten en descripciones de schema.
- **Recorte de desgloses por version** (Tecnica 6): si la tabla resumen y el desglose por version dicen lo mismo, el desglose se elimina.
- **Compresion de bloques motivacionales** (Tecnica 7): las secciones "por que esto importa" se comprimen a la idea tecnica esencial.
- **Compresion de justificaciones de diseño** (Tecnica 8): cuatro parrafos de argumentacion se convierten en una frase con las razones principales.
- **Compresion de listas de casos de uso** (Tecnica 9): casos de uso verbosos de multiples parrafos se comprimen a una linea cada uno.
- **Compresion de mockups ASCII** (Tecnica 10): el arte ASCII costoso en tokens se convierte en una descripcion textual compacta.

Cada edicion debe pasar una validacion estricta: todos los hechos presentes, la AI tomaria decisiones identicas, sin ambiguedad, cada hecho trazable hasta su seccion canonica.

### Pass 3 -- Eliminacion de contenido para humanos (requiere aprobacion, +15-25%)

La pregunta para cada candidato: "Si elimino esto, ¿la AI producira peor output?" Si no, es un objetivo.

- **Tutoriales para principiantes** (Tecnica 11): "Instala tu IDE desde la App Store. Tarda 30-60 minutos..." se convierte en una lista de que instalar, no como.
- **Ejercicios practicos y actividades de aprendizaje** (Tecnica 12): la AI no necesita los ejercicios de aprendizaje de otra persona.
- **Horarios dia a dia** (Tecnica 13): horarios de despertar y pausas para cafe se eliminan. Nombres de funcionalidades y requisitos tecnicos se mantienen.
- **Coaching y contenido motivacional** (Tecnica 14): "No entres en panico" y "¡Celebra!" llevan cero informacion para el modelo.
- **Matrices de validacion** (Tecnica 15): una tabla de 30 filas donde cada funcionalidad dice "SHIP" se convierte en dos lineas.
- **Tablas comparativas de competidores** (Tecnica 16): "Ganamos en todas las dimensiones" motiva a humanos, no a modelos.
- **Ejemplos de workflow para humanos** (Tecnica 17): walkthroughs de "asi es tu dia".

Pass 3 preserva el QUE y el POR QUE. Elimina el COMO-para-humanos.

### Pass 4 -- Reescritura telegrafica (opt-in explicito, +25-40%)

Una reescritura completa del documento en taquigrafia ultra-densa. Cada palabra debe portar informacion; si solo existe para que la prosa fluya bien para un lector humano, se elimina. Para documentos donde la AI es el unico consumidor.

- **Eliminacion de frases puente** (Tecnica 18): "Esto significa que", "lo que permite", "para poder", "es importante notar que" -- todo eliminado.
- **Fragmentos separados por punto y coma** (Tecnica 19): las frases completas se convierten en fragmentos clave-valor. Articulos, "es/son", sujetos de relleno eliminados.
- **Condensacion de multiples frases** (Tecnica 20): cinco frases explicando un concepto se comprimen al hecho esencial.
- **Eliminacion de sujetos implicitos** (Tecnica 21): cuando el titulo de seccion dice "Funcion de busqueda", no hace falta empezar cada frase con "La funcion de busqueda".
- **Notacion compacta** (Tecnica 22): barras para opciones (`immediate/batched/silent`), flechas para flujos (`offline -> local queue -> sync`), dos puntos para relaciones (`Auth: JWT + refresh`).
- **Compresion de patrones estructurales repetitivos** (Tecnica 23): describir el patron una vez, luego usar taquigrafia.

Los LLMs modernos parsean el estilo telegrafico con total fidelidad. Punto y coma, flechas, opciones separadas por barras -- ninguna familia de modelos tiene problemas con esto.

## Modos

Tres modos, cada uno construyendo sobre el anterior:

- **Strict Lossless** (por defecto): Pass 1 + Pass 2. Cada hecho sobrevive. El documento sigue siendo legible por humanos. Ideal para archivos que tanto tu como la AI consultais -- CLAUDE.md, reglas de agente compartidas, especificaciones activas. Seguro, conservador, ahorro real.

- **AI-Lossless**: Añade Pass 3. Elimina tutoriales, coaching, horarios diarios, matrices de validacion -- todo lo que solo ayuda a un lector humano. La AI recibe exactamente la misma señal. Ideal para system prompts e instrucciones de agente que la AI consume pero que tu rara vez relees.

- **AI-Only**: Añade Pass 4. Reescritura completa en estilo telegrafico. Densidad maxima, lectura no placentera. Ideal para prompts de produccion de alto volumen donde cada token ahorrado se multiplica por miles de llamadas diarias.

## Flujo de trabajo

| Pass | Que hace | Aprobacion | Reduccion tipica |
|------|---------|------------|-----------------|
| 1 | Elimina formato Markdown, convierte tablas/listas | Automatica | 10-25% |
| 2 | Deduplica hechos, comprime codigo/JSON/justificaciones | Por elemento | +5-15% |
| 3 | Elimina tutoriales, coaching, horarios, andamiaje humano | Por elemento | +15-25% |
| 4 | Reescritura telegrafica completa, fragmentos en vez de frases | Opt-in explicito | +25-40% |

Los cuatro pases en una especificacion de producto tipica: **50-65% de reduccion**. En un documento de 35.000 palabras, eso son aproximadamente 25.000-32.000 tokens menos por llamada a la API.

## Principios de diseño

- **Lossless por defecto**: Strict Lossless es el modo por defecto. Para compresion mas agresiva se requiere opt-in explicito.
- **Los numeros de seccion son sagrados**: las anclas de referencia cruzada nunca se renumeran, incluso cuando las secciones circundantes se eliminan. "Ver S8.7.3" siempre funciona.
- **Portal de aprobacion**: cada edicion no mecanica se propone con un diff y ahorro estimado. Nada cambia sin tu aprobacion.
- **23 tecnicas con ejemplos**: catalogo completo antes/despues en `references/techniques.md`.
- **12 casos limite cubiertos**: documentos cortos, prompts con mucho codigo, entradas ya comprimidas, textos no ingleses/CJK, URLs embebidas, documentos multilingues, documentos sobre Markdown, prosa creativa/legal, documentos muy grandes, y mas.

## Estructura

```
lossless-prompt-compressor/
├── SKILL.md              — Manual de operacion (~350 lineas)
└── references/
    └── techniques.md     — Catalogo completo de tecnicas con ejemplos antes/despues
```

## Autor

[Wallny](https://github.com/wallmage)
