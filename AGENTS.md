# AGENTS.md — Guía para agentes

Este repositorio es una **plantilla de informes LaTeX** para la Universidad
Privada Domingo Savio (UPDS). Los informes siguen normas APA, tipografía
Arial y papel A4. Esta guía describe cómo trabajar en él y cómo generar
informes nuevos.

## Estructura rápida

| Ruta | Rol | ¿Se edita al generar un informe? |
|------|-----|----------------------------------|
| `main.tex` | Datos del documento + integración | Sí (siempre) |
| `formato/datos-institucion.sty` | Universidad, facultad, carrera, ciudad | Rara vez (una vez por institución) |
| `formato/preambulo.sty` | Paquetes y estilo APA | No |
| `formato/portada.tex` | Portada APA 7 | No |
| `formato/referencias.bib` | Bibliografía BibTeX | Sí (agregar fuentes) |
| `contenido/0N-*.tex` | Un archivo por sección | Sí (el contenido real) |
| `anexos/*.tex` | Anexos | Opcional |
| `figuras/` | Imágenes (`logo.png` va aquí) | Sí (agregar capturas) |
| `Makefile` | Compilación con `make` | No |

## Reglas de oro

1. **Nunca escribir contenido ni estilo en `main.tex`**: ahí solo viven las
   macros de datos y los `\input`.
2. **No tocar `formato/preambulo.sty`** salvo que se cambie el estilo global.
3. Cada sección del informe es un archivo en `contenido/` y se incluye con
   `\input{contenido/NN-nombre}` en `main.tex`.
4. Las imágenes van en `figuras/` y se insertan con las macros del preámbulo.
5. Toda fuente citada con `\citep{clave}` debe existir en
   `formato/referencias.bib`, o aparecerá `[?]`.

## Generar un informe nuevo desde esta plantilla

1. Copiar la carpeta (p. ej. `cp -r . ../informe-N`).
2. En `main.tex`, completar: `\tituloDocumento`, `\equipo`, `\integrantes`,
   `\asignatura`, `\docente`, `\fechaEntrega`.
3. Redactar en `contenido/*.tex`; capítulos se abren en `main.tex` con:

   ```latex
   \caratulaCapitulo{CAPÍTULO II}{TÍTULO DEL CAPÍTULO}
   \input{contenido/02-objetivos}
   ```

4. Agregar capturas/diagramas en `figuras/` y fuentes en
   `formato/referencias.bib`.
5. Compilar con `make` (o la secuencia `pdflatex` + `bibtex` documentada en
   `README.md`). El resultado es `main.pdf`.

## Macros útiles (definidas en `formato/preambulo.sty`)

- `\figuraAPA{label}{título}{imagen.png}{descripción}` — figura con título y
  la línea `FUENTE:` estilo APA, centrada. Si `label` es `{}` no crea
  referencia cruzada.
- `\capturaAPA{label}{título}{archivo.png}{descripción}` — igual que la
  anterior, pero si la imagen no existe muestra un recuadro
  `CAPTURA PENDIENTE` con el nombre esperado del archivo. Ideal durante la
  redacción.
- `\contenidoConFuente{<contenido>}{texto de la fuente}` — para tablas u otro
  contenido con línea FUENTE.
- `\fuenteAPA{texto}` — línea FUENTE a ancho completo (p. ej. terminales).
- `\figuraSegura[0.9]{imagen}{label}` — escala la imagen para que no se
  salga del texto.
- Entorno `tablaAPA` — tablas a una línea de interlineado y cuerpo pequeño.
- Estilo `terminal` de `listings` — bloques de consola con fondo oscuro.
- `\caratulaCapitulo{CAPÍTULO X}{TÍTULO}` — carátula de capítulo a página
  completa con numeración romano/arábiga gestionada sola.
- `\encabezadoCapitulo{TÍTULO}{número}` — encabezado para Conclusiones,
  Referencias y Anexos.

## Convenciones de contenido

- Idioma: español, babel `es-tabla`.
- Márgenes de 1 pulgada, interlineado doble, párrafos sin sangría.
- En tablas usar `booktabs` (`\toprule`, `\midrule`, `\bottomrule`) —
  evitar líneas verticales y `\hline`.
- Referencias y citas en formato APA vía `apacite` (`\citep{...}`).
- Secciones numeradas por capítulo (1.1, 1.2, 2.1, ...).

## Compilación

```bash
make          # compila main.pdf (usa latexmk + bibtex + pasadas extra)
make view     # compila y abre el PDF
make clean    # borra auxiliares, conserva main.pdf
make cleanall # borra también main.pdf
```

Sin `latexmk`:

```bash
pdflatex -interaction=nonstopmode main.tex
bibtex main
pdflatex -interaction=nonstopmode main.tex
pdflatex -interaction=nonstopmode main.tex
```

Los archivos auxiliares (`*.aux`, `*.bbl`, `*.toc`, `main.pdf`, ...) están
en `.gitignore`: **no commitearlos**.
