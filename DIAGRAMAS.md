# Guía de figuras y diagramas

Decisiones para que los agentes generen figuras/diagramas con la **mínima
cantidad de errores**.

## Recomendación principal: TikZ dentro de `\figuraAPA`

Para diagramas de arquitectura, flujo de red, topologías, etc.: escribir
TikZ **en línea** dentro de `\figuraAPA{label}{título}{tikz}{fuente}`.

Ventajas:

- El diagrama es código: diff revisable en git, versionable.
- Se compila con `make`: el agente ve el error de inmediato y lo corrige.
- No requiere herramientas externas (node, inkscape, mmdc).
- Hereda la fuente del documento (sin mismatches de tipografía).

Reglas para agentes (basadas en la práctica habitual y errores reales de
este repo):

1. **Babel y TikZ**: el preámbulo ya desactiva los atajos `<` `>` de
   babel-spanish (`\shorthandoff{<>}`) y carga `\usetikzlibrary{babel}`.
   Aun así, evitar caracteres activos (`"`, `~`, `<`, `>`) dentro de textos
   de nodos; usar `<` y `>` solo dentro de estilos de flecha es seguro con
   la configuración actual, y nunca dentro de `\caption` con acentos
   raros.
2. Usar **nombres de nodos simples** (`cli`, `srv`) y referenciarlos; no
   usar posiciones absolutas numéricas salvo necesidad.
3. Estilos centralizados al inicio del `tikzpicture`:
   `nodo/.style={draw, rounded corners, minimum width=2.6cm, align=center, font=\small}`.
4. Plantilla base que compila:

   ```latex
   \figuraAPA{fig:ejemplo}{Título del diagrama}{%
     \begin{tikzpicture}[
         nodo/.style={draw, rounded corners, minimum width=2.6cm,
                      minimum height=0.9cm, align=center, font=\small},
         linea/.style={-Stealth, thick}]
       \node[nodo] (a) {Origen};
       \node[nodo, right=1.8cm of a] (b) {Destino};
       \draw[linea] (a) -- node[above, font=\scriptsize]{tcp/80} (b);
     \end{tikzpicture}%
   }{Elaboración propia.}
   ```

5. Tras cada edición, correr `make` y corregir el primer error del log
   antes de seguir (los errores posteriores suelen ser en cascada).

## Alternativas (cuándo sí usarlas)

| Caso | Herramienta | Formato a incluir |
|------|-------------|-------------------|
| Capturas de pantalla | guardar en `figuras/` | `\capturaAPA{...}{...}{archivo.png}{...}` |
| Diagramas muy complejos hechos a mano | draw.io / Excalidraw | exportar **PDF** y usar `\includegraphics` |
| Flucharts desde texto | Mermaid (`mmdc`) | exportar PDF/PNG, luego `\includegraphics` |
| Gráficos de datos | pgfplots | TikZ/pgfplots en línea |

Regla: si el diagrama lo puede describir un agente en código, usar **TikZ**;
si no, exportar **PDF vectorial** (nunca PNG a baja resolución).

## Lo más importante para el agente (resumen operativo)

1. **Generar TikZ inline** dentro de `\figuraAPA` (no PNG/SVG externos) para
   diagramas que pueda describir en código: arquitectura, flujo, topología.
2. **Plantilla base**: estilos centralizados (`nodo/.style=`, `linea/.style=`),
   nodos con nombres cortos, flechas con `-Stealth`.
3. **Babel fix ya aplicado**: `\usetikzlibrary{...,babel}` +
   `\AtBeginDocument{\shorthandoff{<>}}` en el preámbulo. No quitarlo —
   es lo que evita el error `Argument of \language@active@arg> has an
   extra }` típico de babel-spanish + TikZ.
4. **Compilar y corregir**: tras editar, `make`; reportar/corregir el
   primer error del log antes de continuar.
5. **Imágenes reales** (capturas): PNG en `figuras/` con `\capturaAPA`.
6. **Diagramas complejos a mano**: exportar PDF desde draw.io/Excalidraw y
   `\includegraphics`. Nunca embeber SVG en pdflatex.
7. **Muchos TikZ**: considerar `\tikzexternalize` para acelerar (ver
   Overleaf docs); no imprescindible en documentos chicos.
8. Prompts detallados funcionan mejor: describir nodos, conexiones,
   etiquetas y layout explícitamente (hallazgo del paper arXiv sobre TikZ
   generado por LLM).

## Hallazgos clave de la investigación (máxima profundidad)

### Paper arXiv:2603.07936 (Mar 2026) — LLM generando TikZ

Estudio con ~190 diagramas dibujados a mano (Teoría de Autómatas),
descripciones generadas por VLM y TikZ generado por LLM (GPT-4o):

- **TikZ compilado supera a imagen generada directamente**: score 4.65/5
  vs 3.6/5 contra los originales. Generar código TikZ y compilarlo es
  objetivamente mejor que pedir al modelo una imagen.
- **Descripciones humanas revisadas mejoran drásticamente el resultado**:
  2.95 → 4.65 con descripción editada. O sea: el cuello de botella no es
  el layout sino la descripción de entrada. Estado de la práctica:
  descripciones estructuradas, revisadas, con todos los estados,
  transiciones y etiquetas explícitas.
- **Tasa de compilación ~131/190 a la primera**; sube con dos trucos:
  1. post-procesar el output del LLM (quitar ``` ```latex ``` y texto
     extra antes/después del código);
  2. incluir los paquetes/librerías que el código referencia.
- **Prompting**: prompts "con la pregunta original" más consistentes;
  one-shot con ejemplo de layout similar al objetivo mejora. Prompt format
  cambia la estructura de salida.
- Errores típicos del LLM: transiciones faltantes/extra, estados de
  aceptación incorrectos, etiquetas/loops mal ubicados.

### Manual oficial PGF/TikZ — librería `babel`

- Babel cambia catcodes de símbolos que TikZ espera normales (`!`, `"`,
  `;` en francés, comillas en alemán, y en español `<`, `>`, `~`, `"`).
- `\usetikzlibrary{babel}` resetea catcodes al inicio de cada
  `\tikzpicture` y los restaura dentro de los nodos. Recomendado siempre.
- **Limitación**: si el `tikzpicture` va como argumento de macro
  (`\figuraAPA{...}{\begin{tikzpicture}...`), TeX ya tokenizó el texto
  con los catcodes viejos → la librería babel **no alcanza**. Solución:
  `\AtBeginDocument{\shorthandoff{<>}}` (ya aplicada en este repo).

### Overleaf docs — compilación rápida y aislada

- `\usetikzlibrary{external}` + `\tikzexternalize[prefix=tikz/]` cachea
  cada TikZ como PDF intermedio (crear carpeta `tikz/` con un archivo
  dummy). Opciones: `optimize command away=\includepdf`.
- Alternativa robusta: proyecto separado con `\documentclass[tikz]{standalone}`
  o el paquete `preview`, compilar los diagramas solos y subir los PDFs.
- Sobre Overleaf: compilar localmente y subir los `.pdf/.md5/.dpth`.

#- ChatGPT/imagen directa: 2.95/5; TikZ desde descripción corregida: 4.65/5.
  Generar **código TikZ y compilar** vence a pedir imagen al modelo.
- Tasa de compilación ~131/190 a la primera para GPT-4o; mejora quitando
  fences (```latex) del output y incluyendo las librerías que referencie.
- Prompt "con la pregunta/contexto del diagrama" > prompt solo-imagen;
  one-shot con ejemplo de layout similar mejora más.
- La revisión humana de la descripción es lo que más mejora el resultado
  (cuello de botella = descripción, no layout).

## Fuentes consultadas

- PGF/TikZ Manual: librería `babel` (https://tikz.dev/library-babel).
- TeX.SE: "Problem with babel and tikz using \draw", "Why do people insist
  on using Tikz when they can use simpler drawing tools?", "Best practices
  to include lots of tikz pictures in an Overleaf project", "TikZ
  externalize...".
- Overleaf docs: "Reducing the compile time for diagrams" (externalize).
- Hacker News (Ask HN): "What do you use to create diagrams?" (draw.io,
  Mermaid, LLM→TikZ, Excalidraw).
- arXiv 2603.07936: "Text to Automata Diagrams: Comparing TikZ Code
  Generation with Direct Image Synthesis" (LLMs generan TikZ desde
  descripción de texto; prompts con ejemplo de layout mejoran resultados).
- draw.io docs: generación de diagramas con LLMs (Mermaid/XML), exportación.
- Medum "Ditch Draw.io": Mermaid como diagrama-como-código versionable.

## Por qué no embeber SVG directo

`pdflatex` no soporta SVG sin `inkscape` y conversiones externas; el
resultado es frágil para agentes. Preferir PDF o PNG.

## Performance

Para documentos grandes con muchos TikZ, activar
`\usetikzlibrary{external}` + `\tikzexternalize` para cachear PDFs
intermedios. No necesario en documentos pequeños.
