# Plantilla de informe LaTeX — UPDS

Plantilla reutilizable para los informes de la Universidad Privada Domingo
Savio (normas APA · tipografía Arial · papel A4).

## Estructura del proyecto

```
plantilla-latex-upds/
├── main.tex                  # Punto de entrada. Define los datos del
│                             #   documento (título, equipo, docente, ...)
│                             #   y ensambla portada, capítulos, referencias.
├── Makefile                  # Compila el PDF con `make` (requiere latexmk).
│
├── formato/                  # FORMATO: todo lo que define la apariencia.
│   ├── preambulo.sty         # Paquetes y configuración APA + A4 + Arial.
│   ├── datos-institucion.sty # Datos compartidos: universidad, facultad,
│   │                         #   carrera y ciudad (editar UNA sola vez).
│   ├── portada.tex           # Portada estilo APA 7. Usa los datos de
│   │                         #   main.tex y datos-institucion.sty.
│   └── referencias.bib       # Bibliografía en BibTeX (formato APA).
│
├── contenido/                # CONTENIDO: el texto real del informe.
│   ├── 01-introduccion.tex   # Capítulo I: Introducción.
│   ├── 02-objetivos.tex      # Capítulo II: Objetivos del trabajo.
│   ├── 03-alcance.tex        # Capítulo II: Alcance y límites.
│   ├── 04-desarrollo.tex     # Capítulo III: Desarrollo del trabajo.
│   └── 05-conclusiones.tex   # Conclusiones.
│
├── anexos/                   # ANEXOS: material complementario.
│   └── anexo-a.tex           # Plantilla de anexo (copiar para anexo B, ...).
│
└── figuras/                  # FIGURAS: imágenes usadas en el informe.
    └── logo.png              # Logo institucional de la portada.
```

### Regla de oro

| Carpeta / archivo          | Qué va ahí                        | ¿Se edita por informe? |
|----------------------------|-----------------------------------|------------------------|
| `main.tex`                 | Datos del documento + integración | Sí                     |
| `formato/datos-institucion.sty` | Universidad, facultad, carrera | No (una vez)          |
| `formato/preambulo.sty`    | Paquetes y estilo APA             | No                     |
| `formato/portada.tex`      | Diseño de la portada              | No                     |
| `formato/referencias.bib`  | Fuentes citadas                   | Sí                     |
| `contenido/*.tex`          | El texto del informe              | Sí                     |
| `figuras/`                 | Capturas, diagramas, logo         | Sí (agregar imágenes)  |
| `Makefile`                 | Comandos de compilación           | No                     |

## Cómo usarla para un nuevo informe

1. Copiar la carpeta:

   ```bash
   cp -r plantilla-latex-upds ../informe-actividad-N
   cd ../informe-actividad-N
   ```

2. Editar los datos del documento en `main.tex` (título, equipo,
   integrantes, asignatura, docente, fecha).

3. Redactar el contenido en `contenido/*.tex` y agregar las imágenes en
   `figuras/`.

4. Agregar las fuentes citadas en `formato/referencias.bib`.

## Guía de compilación

### Con `make` (recomendado, requiere `latexmk`)

```bash
make          # compila main.pdf
make view     # compila y abre el PDF
make clean    # borra archivos auxiliares (conserva el PDF)
make cleanall # borra también el PDF
```

### Manualmente (si no tienes `latexmk`)

```bash
pdflatex -interaction=nonstopmode main.tex
bibtex main
pdflatex -interaction=nonstopmode main.tex
pdflatex -interaction=nonstopmode main.tex
```

Se necesitan dos pasadas de `pdflatex` después de `bibtex` para que el
índice, las referencias cruzadas y la bibliografía queden correctos.

El PDF generado es `main.pdf`.

## Requisitos

- TeX Live (o MiKTeX) con los paquetes: `babel-spanish`, `helvet`,
  `geometry`, `setspace`, `fancyhdr`, `titlesec`, `booktabs`, `caption`,
  `graphicx`, `tikz`, `listings`, `hyperref`, `apacite`, `tocloft`,
  `microtype`, `enumitem`, `amsmath`.
- `latexmk` (opcional, pero recomendado).
