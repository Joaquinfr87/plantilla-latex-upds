# Guía de instalación (Linux)

Lo **mínimo necesario** para compilar los informes de esta plantilla sin
descargar los ~5 GB de TeX Live completo.

## Debian / Ubuntu

```bash
sudo apt update
sudo apt install --no-install-recommends \
    texlive-latex-base \
    texlive-latex-recommended \
    texlive-latex-extra \
    texlive-lang-spanish \
    texlive-pictures \
    texlive-fonts-recommended \
    texlive-bibtex-extra \
    latexmk
```

Paquetes LaTeX que usa la plantilla y dónde vienen:

| Necesidad | Paquete apt |
|-----------|-------------|
| `babel` español, `geometry`, `helvet` | `texlive-latex-base`, `texlive-fonts-recommended` |
| `setspace`, `fancyhdr`, `titlesec`, `caption`, `enumitem`, `microtype`, `apacite` | `texlive-latex-recommended` / `texlive-latex-extra` |
| `tikz`, `graphicx` | `texlive-pictures` |
| `apacite` (referencias APA) y `bibtex` | `texlive-bibtex-extra` |
| `latexmk` (compilación automática) | `latexmk` |

Si algún paquete falta al compilar, instálalo puntualmente:

```bash
sudo apt install texlive-<grupo>   # p. ej. texlive-fonts-extra
```

## Fedora

```bash
sudo dnf install texlive-scheme-medium texlive-apacite latexmk
```

## Arch Linux

```bash
sudo pacman -S texlive-basic texlive-latex texlive-latexextra \
    texlive-langspanish texlive-pictures latexmk
```

## Verificar

```bash
pdflatex --version
latexmk --version
make          # debe compilar main.pdf sin errores
```

## Iconos del escritorio / visor

Cualquier visor funciona; uno ligero:

```bash
sudo apt install atril     # o okular, evince, zathura
```
