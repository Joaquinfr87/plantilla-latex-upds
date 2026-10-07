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
