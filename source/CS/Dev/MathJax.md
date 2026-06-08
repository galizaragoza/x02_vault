#reference #Matematicas

Guía completa de sintaxis **MathJax** (motor LaTeX que Obsidian usa para renderizar matemáticas). En Obsidian: `$...$` para fórmulas **en línea** y `$$...$$` para fórmulas en **bloque** (centradas, en su propia línea).

> [!tip] Obsidian usa MathJax 3. Soporta la mayoría de comandos de LaTeX matemático, pero **no** texto LaTeX general (`\section`, `\begin{document}`...). Solo el modo matemático.

# Sintaxis básica

| Construcción | Sintaxis | Resultado |
|--------------|----------|-----------|
| En línea | `$E = mc^2$` | $E = mc^2$ |
| En bloque | `$$E = mc^2$$` | (centrado en línea propia) |
| Subíndice | `x_1`, `x_{10}` | $x_1$, $x_{10}$ |
| Superíndice | `x^2`, `x^{10}` | $x^2$, $x^{10}$ |
| Sub+super | `x_i^2` | $x_i^2$ |
| Agrupar | `{...}` (no se renderiza) | agrupa varios caracteres |
| Espacio forzado | `\ `, `\quad`, `\qquad` | espacios de distinto ancho |
| Texto dentro | `\text{...}` | texto normal sin cursiva |

> Los subíndices/superíndices de más de un carácter **requieren llaves**: `x^{2n}` (no `x^2n`, que da $x^2n$).

# Fracciones, raíces y binomios

| Construcción | Sintaxis | Resultado |
|--------------|----------|-----------|
| Fracción | `\frac{a}{b}` | $\frac{a}{b}$ |
| Fracción en línea | `\tfrac{a}{b}` | pequeña |
| Fracción display | `\dfrac{a}{b}` | grande |
| Fracción continua | `\cfrac{a}{b}` | para fracciones anidadas |
| Raíz cuadrada | `\sqrt{x}` | $\sqrt{x}$ |
| Raíz n-ésima | `\sqrt[n]{x}` | $\sqrt[n]{x}$ |
| Binomio | `\binom{n}{k}` | $\binom{n}{k}$ |

```latex
$$\frac{-b \pm \sqrt{b^2 - 4ac}}{2a}$$
```

# Operadores y símbolos comunes

| Categoría | Sintaxis | Símbolo |
|-----------|----------|---------|
| Multiplicación | `\times`, `\cdot`, `\ast` | $\times$ $\cdot$ $\ast$ |
| División | `\div`, `\setminus` | $\div$ $\setminus$ |
| Más/menos | `\pm`, `\mp` | $\pm$ $\mp$ |
| Igualdad | `=`, `\neq`, `\equiv`, `\approx` | $=$ $\neq$ $\equiv$ $\approx$ |
| Desigualdad | `\leq`, `\geq`, `\ll`, `\gg` | $\leq$ $\geq$ $\ll$ $\gg$ |
| Proporción | `\propto`, `\sim`, `\cong` | $\propto$ $\sim$ $\cong$ |
| Composición | `\circ`, `\bullet`, `\oplus`, `\otimes` | $\circ$ $\oplus$ $\otimes$ |
| Infinito | `\infty`, `\partial`, `\nabla` | $\infty$ $\partial$ $\nabla$ |
| Punto/elipsis | `\cdot`, `\ldots`, `\cdots`, `\vdots`, `\ddots` | $\ldots$ $\cdots$ $\vdots$ |

# Letras griegas

| Minúscula | Sintaxis | Mayúscula | Sintaxis |
|-----------|----------|-----------|----------|
| $\alpha$ | `\alpha` | $\Gamma$ | `\Gamma` |
| $\beta$ | `\beta` | $\Delta$ | `\Delta` |
| $\gamma$ | `\gamma` | $\Theta$ | `\Theta` |
| $\delta$ | `\delta` | $\Lambda$ | `\Lambda` |
| $\epsilon$ / $\varepsilon$ | `\epsilon` / `\varepsilon` | $\Pi$ | `\Pi` |
| $\theta$ / $\vartheta$ | `\theta` / `\vartheta` | $\Sigma$ | `\Sigma` |
| $\lambda$ | `\lambda` | $\Phi$ | `\Phi` |
| $\mu$ | `\mu` | $\Psi$ | `\Psi` |
| $\pi$ | `\pi` | $\Omega$ | `\Omega` |
| $\sigma$ | `\sigma` | $\Xi$ | `\Xi` |
| $\phi$ / $\varphi$ | `\phi` / `\varphi` | $\Upsilon$ | `\Upsilon` |
| $\omega$ | `\omega` | | |

> Las griegas mayúsculas que coinciden con latinas (A, B, E...) se escriben con letra latina normal.

# Sumatorios, productos, integrales y límites

| Construcción | Sintaxis | Resultado |
|--------------|----------|-----------|
| Sumatorio | `\sum_{i=1}^{n}` | $\sum_{i=1}^{n}$ |
| Productorio | `\prod_{i=1}^{n}` | $\prod_{i=1}^{n}$ |
| Integral | `\int_{a}^{b}` | $\int_{a}^{b}$ |
| Integral doble/triple | `\iint`, `\iiint` | $\iint$ $\iiint$ |
| Integral cerrada | `\oint` | $\oint$ |
| Límite | `\lim_{x \to 0}` | $\lim_{x \to 0}$ |
| Unión / intersección | `\bigcup`, `\bigcap` | $\bigcup$ $\bigcap$ |

```latex
$$\sum_{n=1}^{\infty} \frac{1}{n^2} = \frac{\pi^2}{6}$$
```

> En `$$...$$` (display) los límites van arriba/abajo; en línea `$...$` van al lado. Forzar con `\limits` / `\nolimits`.

# Funciones y nombres de operadores

| Tipo | Sintaxis | Resultado |
|------|----------|-----------|
| Trigonométricas | `\sin`, `\cos`, `\tan`, `\cot`, `\sec`, `\csc` | $\sin \cos \tan$ |
| Inversas/hiperbólicas | `\arcsin`, `\sinh`, `\cosh` | $\arcsin \sinh$ |
| Logaritmos | `\log`, `\ln`, `\log_2` | $\log \ln \log_2$ |
| Límite/máx/mín | `\lim`, `\max`, `\min`, `\sup`, `\inf` | $\max \min$ |
| Otros | `\exp`, `\gcd`, `\det`, `\dim`, `\deg` | $\exp \gcd \det$ |
| Operador propio | `\operatorname{mcd}` | $\operatorname{mcd}$ |

> Usar `\sin` en vez de `sin` evita que se renderice en cursiva ($sin$ vs $\sin$).

# Flechas y relaciones lógicas

| Sintaxis | Símbolo | Sintaxis | Símbolo |
|----------|---------|----------|---------|
| `\to` / `\rightarrow` | $\to$ | `\leftarrow` | $\leftarrow$ |
| `\Rightarrow` | $\Rightarrow$ | `\Leftarrow` | $\Leftarrow$ |
| `\leftrightarrow` | $\leftrightarrow$ | `\Leftrightarrow` / `\iff` | $\iff$ |
| `\mapsto` | $\mapsto$ | `\implies` | $\implies$ |
| `\uparrow` / `\downarrow` | $\uparrow \downarrow$ | `\longrightarrow` | $\longrightarrow$ |
| `\forall` | $\forall$ | `\exists` / `\nexists` | $\exists \nexists$ |
| `\neg` | $\neg$ | `\land` / `\lor` | $\land \lor$ |
| `\therefore` | $\therefore$ | `\because` | $\because$ |

# Conjuntos y teoría de números

| Sintaxis | Símbolo | Significado |
|----------|---------|-------------|
| `\in`, `\notin` | $\in$ $\notin$ | pertenece / no pertenece |
| `\subset`, `\subseteq` | $\subset$ $\subseteq$ | subconjunto |
| `\supset`, `\supseteq` | $\supset$ $\supseteq$ | superconjunto |
| `\cup`, `\cap` | $\cup$ $\cap$ | unión / intersección |
| `\emptyset`, `\varnothing` | $\emptyset$ $\varnothing$ | conjunto vacío |
| `\mathbb{N}` | $\mathbb{N}$ | naturales |
| `\mathbb{Z}` | $\mathbb{Z}$ | enteros |
| `\mathbb{Q}` | $\mathbb{Q}$ | racionales |
| `\mathbb{R}` | $\mathbb{R}$ | reales |
| `\mathbb{C}` | $\mathbb{C}$ | complejos |
| `\equiv \pmod{n}` | $\equiv \pmod{n}$ | congruencia modular |

> `\mathbb{}` requiere el paquete AMS, activo por defecto en Obsidian.

# Acentos, decoradores y vectores

| Construcción | Sintaxis | Resultado |
|--------------|----------|-----------|
| Vector | `\vec{v}` | $\vec{v}$ |
| Sombrero | `\hat{x}` | $\hat{x}$ |
| Barra | `\bar{x}`, `\overline{xy}` | $\bar{x}$ $\overline{xy}$ |
| Tilde | `\tilde{x}`, `\widetilde{xy}` | $\tilde{x}$ |
| Punto | `\dot{x}`, `\ddot{x}` | $\dot{x}$ $\ddot{x}$ |
| Flecha sobre | `\overrightarrow{AB}` | $\overrightarrow{AB}$ |
| Llave arriba/abajo | `\overbrace{}`, `\underbrace{}` | agrupación con etiqueta |
| Subrayado | `\underline{x}` | $\underline{x}$ |

```latex
$$\underbrace{1 + 2 + \cdots + n}_{n \text{ términos}} = \frac{n(n+1)}{2}$$
```

# Paréntesis y delimitadores

| Tipo | Sintaxis | Resultado |
|------|----------|-----------|
| Paréntesis auto-ajustables | `\left( ... \right)` | crecen con el contenido |
| Corchetes | `\left[ ... \right]` | $\left[ x \right]$ |
| Llaves | `\left\{ ... \right\}` | $\left\{ x \right\}$ |
| Valor absoluto | `\left\| x \right\|` | $\lvert x \rvert$ |
| Norma | `\left\Vert x \right\Vert` | $\lVert x \rVert$ |
| Suelo / techo | `\lfloor x \rfloor`, `\lceil x \rceil` | $\lfloor x \rfloor$ $\lceil x \rceil$ |
| Delimitador invisible | `\left. ... \right)` | un lado sin dibujar |
| Tamaños manuales | `\big( \Big( \bigg( \Bigg(` | tamaños fijos crecientes |

> `\left` y `\right` siempre van **en pareja**; usar `\left.` o `\right.` cuando solo se necesita un lado.

# Matrices y arrays

| Entorno | Delimitador |
|---------|-------------|
| `matrix` | sin delimitadores |
| `pmatrix` | paréntesis `( )` |
| `bmatrix` | corchetes `[ ]` |
| `Bmatrix` | llaves `{ }` |
| `vmatrix` | barras `\| \|` (determinante) |
| `Vmatrix` | doble barra `‖ ‖` |

```latex
$$\begin{pmatrix} a & b \\ c & d \end{pmatrix}$$
```

$$\begin{pmatrix} a & b \\ c & d \end{pmatrix}$$

- Columnas separadas por `&`, filas por `\\`.
- `\cdots`, `\vdots`, `\ddots` para puntos suspensivos en matrices grandes.

# Casos y ecuaciones alineadas

Función a trozos con `cases`:

```latex
$$f(x) = \begin{cases} x^2 & \text{si } x \geq 0 \\ -x & \text{si } x < 0 \end{cases}$$
```

$$f(x) = \begin{cases} x^2 & \text{si } x \geq 0 \\ -x & \text{si } x < 0 \end{cases}$$

Alinear varias ecuaciones por el `=` con `aligned` (el `&` marca el punto de alineación):

```latex
$$\begin{aligned}
(a+b)^2 &= a^2 + 2ab + b^2 \\
        &= a^2 + b^2 + 2ab
\end{aligned}$$
```

# Fuentes y estilos

| Estilo | Sintaxis | Resultado |
|--------|----------|-----------|
| Negrita matemática | `\mathbf{A}` | $\mathbf{A}$ |
| Pizarra (blackboard) | `\mathbb{R}` | $\mathbb{R}$ |
| Caligráfica | `\mathcal{L}` | $\mathcal{L}$ |
| Fraktur | `\mathfrak{g}` | $\mathfrak{g}$ |
| Sans-serif | `\mathsf{X}` | $\mathsf{X}$ |
| Monospace | `\mathtt{x}` | $\mathtt{x}$ |
| Romana (sin cursiva) | `\mathrm{d}x` | $\mathrm{d}x$ |
| Negrita de símbolos | `\boldsymbol{\alpha}` | $\boldsymbol{\alpha}$ |
| Color (Obsidian) | `\color{red}{x}` | texto en rojo |

# Espaciado fino

| Sintaxis | Espacio |
|----------|---------|
| `\,` | muy pequeño (thin) |
| `\:` | medio |
| `\;` | grande (thick) |
| `\ ` (backslash+espacio) | espacio normal |
| `\quad` | un cuadratín (≈ ancho de M) |
| `\qquad` | doble cuadratín |
| `\!` | espacio negativo (acerca) |

# Trucos y errores comunes en Obsidian

| Problema | Causa | Solución |
|----------|-------|----------|
| `$...$` no renderiza | Espacio tras `$` de apertura: `$ x$` | Sin espacio: `$x$` |
| `_` o `*` rompen el texto | Markdown interpreta `_`/`*` fuera de math | Mantenerlos dentro de `$...$` |
| Llave literal `{` no aparece | `{ }` agrupan, no se imprimen | Escapar: `\{` y `\}` |
| `\\` da error en `$...$` en línea | Saltos de línea solo en entornos | Usar `aligned`/`cases` en `$$...$$` |
| `%` corta la fórmula | Es comentario en LaTeX | Escapar: `\%` |
| Símbolo `$` literal | Delimitador de math | Escapar fuera de math: `\$` |

> Para numerar/etiquetar ecuaciones (`\tag{}`, `\label`) el soporte es parcial; `\tag{1}` funciona en `$$...$$` para añadir una etiqueta manual a la derecha.

# Recursos
### [MathJax Docs — Supported TeX/LaTeX](https://docs.mathjax.org/en/latest/input/tex/macros/index.html)
### [Obsidian Help — LaTeX / MathJax](https://help.obsidian.md/Editing+and+formatting/Advanced+formatting+syntax#Math)
### [Detexify — dibuja un símbolo y obtén su comando](https://detexify.kirelabs.org/classify.html)
### [LaTeX/Mathematics — Wikibooks](https://en.wikibooks.org/wiki/LaTeX/Mathematics)
### [The Comprehensive LaTeX Symbol List](https://ctan.org/pkg/comprehensive)
