#reference #Matematicas

Referencia completa de **MathJax** tal y como lo implementa Obsidian: delimitadores, sintaxis, catálogo de comandos por paquete, macros, depuración y limitaciones reales. Pensada como refresco rápido: cada sección es autocontenida y las tablas son copiables.

> [!info] Alcance verificado
> El contenido de esta nota se ha contrastado contra el motor que Obsidian carga realmente (`lib/mathjax/tex-chtml-full.js` dentro de `obsidian.asar`, versión **MathJax 3.2.2**). La sección [[#21. Comandos no soportados]] lista comandos habituales de LaTeX/KaTeX que **no** existen en ese motor: si un comando no aparece aquí, probablemente no funcione.

# 1. El motor: qué ejecuta Obsidian exactamente

| Aspecto | Valor real en Obsidian |
|---|---|
| Motor | MathJax **3.2.2** (no KaTeX, no LaTeX real) |
| Bundle | `tex-chtml-full.js` (entrada TeX completa + salida CHTML) |
| Salida | **CHTML** (HTML + CSS con fuentes MathJax_*), no SVG |
| Menú contextual | Desactivado (`enableMenu: false`): no hay «Show Math As…» |
| Detección de delimitadores | La hace **Obsidian**, no MathJax |
| Numeración automática | Desactivada (`tags: 'none'`): no hay números de ecuación |

**Consecuencia práctica**: MathJax procesa únicamente *modo matemático*. No existe preámbulo, ni `\usepackage`, ni `\documentclass`, ni entornos de documento (`figure`, `table`, `theorem`), ni `\includegraphics`, ni bibliografía. Todo lo que va entre `$…$` o `$$…$$` se compila como si estuviera dentro de un `$…$` de LaTeX.

## Paquetes TeX cargados por defecto

Obsidian usa el conjunto `AllPackages` completo. Están activos **sin hacer nada**:

```
base      action     ams        amscd     bbox      boldsymbol
braket    bussproofs cancel     cases     centernot color
colortbl  empheq     enclose    extpfeil  gensymb   html
mathtools mhchem     newcommand noerrors  noundefined upgreek
unicode   verb       configmacros tagformat textcomp textmacros
require
```

## Paquetes disponibles pero NO activos

Están compilados dentro del bundle pero fuera del conjunto por defecto. Se activan en caliente con `\require{...}` (funciona sin conexión, no descarga nada):

| Paquete | Activación | Aporta |
|---|---|---|
| `physics` | `\require{physics}` | `\dv`, `\pdv`, `\qty`, `\grad`, `\curl`, `\comm`… |
| `colorv2` | `\require{colorv2}` | Semántica MathJax 2 de `\color{rojo}{texto}` |
| `setoptions` | `\require{setoptions}` | `\setOptions` para ajustar opciones del parser |
| `autoload` | `\require{autoload}` | Carga diferida de extensiones |

```latex
$$\require{physics} \dv{f}{x} = \lim_{h\to 0}\frac{f(x+h)-f(x)}{h}$$
```

> [!tip] Ámbito de `\require`
> Una vez ejecutado, el paquete queda activo en la instancia de MathJax **durante toda la sesión de la app**, no solo en esa nota. Para que la nota sea autosuficiente al abrirla en frío, deja el `\require` en el primer bloque matemático de la nota.

# 2. Delimitadores y reglas de parseo en Obsidian

Obsidian desactiva el escáner de MathJax (`inlineMath: []`, `displayMath: []`, `processEscapes: false`, `processEnvironments: false`) y usa su propio parser Markdown. Esto cambia las reglas respecto a un MathJax «estándar».

| Sintaxis | ¿Funciona? | Notas |
|---|---|---|
| `$…$` | Sí | Matemática en línea |
| `$$…$$` | Sí | Bloque, centrado, `display` |
| `\(…\)` | **No** | Delimitador desactivado en Obsidian |
| `\[…\]` | **No** | Delimitador desactivado en Obsidian |
| `\begin{align}` suelto | **No** | `processEnvironments: false`; hay que envolverlo en `$$…$$` |

## Reglas exactas del delimitador en línea

Extraídas del parser de Obsidian. Son la causa del 90 % de los «no me renderiza»:

1. El `$` de apertura **no puede ir seguido de espacio ni tabulador**.
2. El `$` de cierre **no puede ir precedido de espacio ni tabulador**.
3. El `$` de cierre **no puede ir seguido de un dígito `0`–`9`** (protección anti-precios).
4. `\$` produce un dólar literal y nunca abre fórmula.
5. Dentro de la fórmula, `\` escapa el carácter siguiente: `$\$$` es válido.

```markdown
$x^2$        OK   renderiza
$ x^2$       FALLA espacio tras la apertura
$x^2 $       FALLA espacio antes del cierre
$x^2$3       FALLA dígito pegado tras el cierre
$x^2$ 3      OK   con espacio de separación sí renderiza
```

## Reglas del delimitador de bloque

- Los espacios tras la apertura y antes del cierre **sí** están permitidos: `$$ x^2 $$` es válido.
- Tras el `$$` de cierre, Obsidian consume los espacios en blanco hasta el final de línea.
- Para que el bloque se comporte como bloque real (centrado, márgenes), déjalo en líneas propias:

```markdown
$$
\int_0^\infty e^{-x^2}\,dx = \frac{\sqrt{\pi}}{2}
$$
```

# 3. Fundamentos de sintaxis

| Construcción | Sintaxis | Resultado |
|---|---|---|
| Subíndice | `x_1`, `x_{10}` | $x_1$, $x_{10}$ |
| Superíndice | `x^2`, `x^{10}` | $x^2$, $x^{10}$ |
| Ambos | `x_i^2`, `{}_a^b X` | $x_i^2$, ${}_a^b X$ |
| Agrupación | `{...}` | Agrupa sin imprimir |
| Prima | `f'`, `f''`, `f^{\prime}` | $f'$, $f''$ |
| Texto | `\text{si } x>0` | $\text{si } x>0$ |
| Texto anidado con math | `\text{cuando $x>0$}` | Permite volver a modo math |
| Comentario | `% esto se ignora` | No se renderiza |

## Caracteres que hay que escapar

| Carácter | Escape | Motivo |
|---|---|---|
| `\` | `\backslash` | `\\` es salto de línea |
| `{` `}` | `\{` `\}` | Agrupan |
| `%` | `\%` | Inicia comentario |
| `$` | `\$` | Delimitador |
| `_` `^` | `\_` `\^` | Sub/superíndice |
| `&` | `\&` | Separador de columna |
| `#` | `\#` | Parámetro de macro |
| `~` | `\sim` (símbolo) | En TeX es espacio irrompible |

> [!warning] Sub/superíndices de más de un carácter exigen llaves
> `x^2n` da $x^2n$ (solo el `2` sube). Lo correcto es `x^{2n}`.

## Estilos matemáticos

| Comando | Efecto |
|---|---|
| `\displaystyle` | Fuerza estilo de bloque (límites arriba/abajo, fracciones grandes) |
| `\textstyle` | Fuerza estilo en línea |
| `\scriptstyle` | Tamaño de subíndice |
| `\scriptscriptstyle` | Tamaño de sub-subíndice |
| `\limits` / `\nolimits` | Fuerza la posición de los límites en operadores grandes |

```latex
$\displaystyle\sum_{i=1}^{n} i$   ← límites arriba/abajo incluso en línea
```

## Tamaños de fuente

`\tiny` · `\scriptsize` · `\small` · `\normalsize` · `\large` · `\Large` · `\LARGE` · `\huge` · `\Huge`

Actúan como conmutadores hasta el fin del grupo: `{\Large x} y`.

# 4. Espaciado

| Comando | Ancho | Uso típico |
|---|---|---|
| `\,` | 3/18 em (thin) | Antes de `dx` en integrales |
| `\:` | 4/18 em (medium) | Separación media |
| `\;` | 5/18 em (thick) | Separación amplia |
| `\!` | −3/18 em | Acercar (negativo) |
| `\ ` (barra+espacio) | Espacio de palabra | Dentro de fórmula |
| `\quad` | 1 em | Separar cláusulas |
| `\qquad` | 2 em | Separar ecuaciones |
| `\enspace` | 0,5 em | Ajuste fino |
| `\thinspace` | = `\,` | Forma nombrada |
| `\negthinspace` | = `\!` | Forma nombrada |
| `\negmedspace` | −4/18 em | Ajuste fino negativo |
| `\negthickspace` | −5/18 em | Ajuste fino negativo |
| `\nobreakspace` | Espacio irrompible | Equivale a `~` de LaTeX |
| `\space` | Espacio de texto | Dentro de `\text` |
| `\hspace{2em}` | Arbitrario | Admite `pt`, `em`, `ex`, `cm`, `mm`, `in`, `mu` |
| `\kern 5pt` | Arbitrario | Primitiva TeX |
| `\mkern 5mu` | Arbitrario en `mu` | Unidades matemáticas |
| `\mskip`, `\hskip` | Arbitrario | Equivalentes de bajo nivel |
| `\mspace{5mu}` | Arbitrario | Forma MathML |

> `\medspace` y `\thickspace` **no existen** en este motor: usa `\:` y `\;`.

## Espaciado invisible y composición

| Comando | Efecto |
|---|---|
| `\phantom{X}` | Reserva ancho y alto sin dibujar |
| `\hphantom{X}` | Reserva solo el ancho |
| `\vphantom{X}` | Reserva solo la altura |
| `\smash{X}` | Dibuja sin ocupar altura (`\smash[t]`, `\smash[b]`) |
| `\llap{X}` | Dibuja a la izquierda sin ocupar ancho |
| `\rlap{X}` | Dibuja a la derecha sin ocupar ancho |
| `\strut` | Puntal invisible de altura de línea |
| `\mathstrut` | Puntal con la altura de un paréntesis |
| `\raise{2pt}{X}` | Sube el contenido |
| `\lower{2pt}{X}` | Baja el contenido |

# 5. Fracciones, raíces y binomios

| Construcción | Sintaxis | Resultado |
|---|---|---|
| Fracción | `\frac{a}{b}` | $\frac{a}{b}$ |
| Fracción en línea | `\tfrac{a}{b}` | $\tfrac{a}{b}$ |
| Fracción display | `\dfrac{a}{b}` | $\dfrac{a}{b}$ |
| Fracción continua | `\cfrac{a}{b}` | Anidamiento sin encoger |
| Continua alineada | `\cfrac[l]{a}{b}`, `\cfrac[r]{a}{b}` | Alinea el numerador |
| Fracción genérica | `\genfrac{(}{)}{0pt}{0}{a}{b}` | Control total (delim. izq., der., grosor, estilo) |
| Sin barra | `{a \atop b}` | $a \atop b$ |
| Barra TeX | `{a \over b}` | Estilo antiguo |
| Con delimitadores | `{a \brace b}`, `{a \brack b}` | Llaves / corchetes |
| Grosor manual | `{a \above 1pt b}` | Barra personalizada |
| Binomio | `\binom{n}{k}` | $\binom{n}{k}$ |
| Binomio in/display | `\tbinom{n}{k}`, `\dbinom{n}{k}` | Variantes de tamaño |
| Binomio TeX | `{n \choose k}` | $n \choose k$ |
| Raíz cuadrada | `\sqrt{x}` | $\sqrt{x}$ |
| Raíz n-ésima | `\sqrt[n]{x}` | $\sqrt[n]{x}$ |
| Raíz TeX | `\root n \of x` | Equivalente |
| Signo de raíz | `\surd` | $\surd$ |
| Numerador partido | `\splitfrac{a+b}{+c}` | mathtools |
| Numerador partido display | `\splitdfrac{a+b}{+c}` | mathtools |

```latex
$$x = \frac{-b \pm \sqrt{b^2-4ac}}{2a}
\qquad
\cfrac{1}{1+\cfrac{1}{1+\cfrac{1}{1+\ddots}}}$$
```

# 6. Alfabetos

## Griego minúsculas

| Cmd | Símb | Cmd | Símb | Cmd | Símb |
|---|---|---|---|---|---|
| `\alpha` | $\alpha$ | `\iota` | $\iota$ | `\sigma` | $\sigma$ |
| `\beta` | $\beta$ | `\kappa` | $\kappa$ | `\varsigma` | $\varsigma$ |
| `\gamma` | $\gamma$ | `\varkappa` | $\varkappa$ | `\tau` | $\tau$ |
| `\delta` | $\delta$ | `\lambda` | $\lambda$ | `\upsilon` | $\upsilon$ |
| `\epsilon` | $\epsilon$ | `\mu` | $\mu$ | `\phi` | $\phi$ |
| `\varepsilon` | $\varepsilon$ | `\nu` | $\nu$ | `\varphi` | $\varphi$ |
| `\zeta` | $\zeta$ | `\xi` | $\xi$ | `\chi` | $\chi$ |
| `\eta` | $\eta$ | `\omicron` | $\omicron$ | `\psi` | $\psi$ |
| `\theta` | $\theta$ | `\pi` | $\pi$ | `\omega` | $\omega$ |
| `\vartheta` | $\vartheta$ | `\varpi` | $\varpi$ | `\digamma` | $\digamma$ |
| `\rho` | $\rho$ | `\varrho` | $\varrho$ | | |

## Griego mayúsculas

`\Gamma` $\Gamma$ · `\Delta` $\Delta$ · `\Theta` $\Theta$ · `\Lambda` $\Lambda$ · `\Xi` $\Xi$ · `\Pi` $\Pi$ · `\Sigma` $\Sigma$ · `\Upsilon` $\Upsilon$ · `\Phi` $\Phi$ · `\Psi` $\Psi$ · `\Omega` $\Omega$

> Las mayúsculas homógrafas del latín (A, B, E, Z, H, I, K, M, N, O, P, T, X) se escriben con la letra latina; en cursiva matemática por defecto, romana con `\mathrm{A}`.

## Griego vertical (`upgreek`)

Para constantes y unidades, donde la cursiva es tipográficamente incorrecta.

- Minúsculas: `\upalpha` `\upbeta` `\upgamma` `\updelta` `\upepsilon` `\upzeta` `\upeta` `\uptheta` `\upiota` `\upkappa` `\uplambda` `\upmu` `\upnu` `\upxi` `\upomicron` `\uppi` `\uprho` `\upsigma` `\uptau` `\upupsilon` `\upphi` `\upchi` `\uppsi` `\upomega`
- Mayúsculas: `\Upgamma` `\Updelta` `\Uptheta` `\Uplambda` `\Upxi` `\Uppi` `\Upsigma` `\Upupsilon` `\Upphi` `\Uppsi` `\Upomega`

## Hebreo y letras especiales

`\aleph` $\aleph$ · `\beth` $\beth$ · `\gimel` $\gimel$ · `\daleth` $\daleth$ · `\ell` $\ell$ · `\hbar` $\hbar$ · `\hslash` $\hslash$ · `\imath` $\imath$ · `\jmath` $\jmath$ · `\wp` $\wp$ · `\Re` $\Re$ · `\Im` $\Im$ · `\eth` $\eth$ · `\mho` $\mho$ · `\Finv` $\Finv$ · `\Game` $\Game$

## Familias tipográficas

| Comando | Resultado | Uso |
|---|---|---|
| `\mathrm{d}` | $\mathrm{d}$ | Diferenciales, unidades |
| `\mathit{diff}` | $\mathit{diff}$ | Cursiva de texto |
| `\mathnormal{x}` | $\mathnormal{x}$ | Cursiva matemática por defecto |
| `\mathbf{A}` | $\mathbf{A}$ | Vectores, matrices |
| `\boldsymbol{\alpha}` | $\boldsymbol{\alpha}$ | Negrita de símbolos y griegas |
| `\pmb{\nabla}` | $\pmb{\nabla}$ | Negrita por superposición |
| `\mathbb{R}` | $\mathbb{R}$ | Conjuntos numéricos |
| `\mathcal{L}` | $\mathcal{L}$ | Transformadas, categorías |
| `\mathscr{F}` | $\mathscr{F}$ | Script |
| `\mathfrak{g}` | $\mathfrak{g}$ | Álgebras de Lie, ideales |
| `\mathsf{X}` | $\mathsf{X}$ | Sans-serif |
| `\mathtt{x}` | $\mathtt{x}$ | Monoespaciada |

Formas cortas heredadas (conmutadores, no toman argumento): `\rm` `\bf` `\it` `\sf` `\tt` `\cal` `\frak` `\scr` `\Bbb` `\bold`.

## Modo texto dentro de matemáticas

| Comando | Efecto |
|---|---|
| `\text{...}` | Texto en la fuente del entorno |
| `\textrm{...}` | Texto romano |
| `\textbf{...}` | Texto negrita |
| `\textit{...}` | Texto cursiva |
| `\textsf{...}` | Texto sans-serif |
| `\texttt{...}` | Texto monoespaciado |
| `\textnormal{...}`, `\textup{...}` | Texto normal / vertical |
| `\emph{...}` | Énfasis (paquete `textmacros`) |
| `\mbox{...}`, `\hbox{...}` | Caja horizontal sin ruptura |

# 7. Operadores grandes, límites y funciones

## Operadores con límites

| Cmd | Símb | Cmd | Símb |
|---|---|---|---|
| `\sum` | $\sum$ | `\bigcup` | $\bigcup$ |
| `\prod` | $\prod$ | `\bigcap` | $\bigcap$ |
| `\coprod` | $\coprod$ | `\bigsqcup` | $\bigsqcup$ |
| `\int` | $\int$ | `\bigvee` | $\bigvee$ |
| `\iint` | $\iint$ | `\bigwedge` | $\bigwedge$ |
| `\iiint` | $\iiint$ | `\bigodot` | $\bigodot$ |
| `\iiiint` | $\iiiint$ | `\bigoplus` | $\bigoplus$ |
| `\idotsint` | $\idotsint$ | `\bigotimes` | $\bigotimes$ |
| `\oint` | $\oint$ | `\biguplus` | $\biguplus$ |

Modificadores:

| Sintaxis | Efecto |
|---|---|
| `\sum\limits_{i=1}^n` | Límites arriba/abajo aunque sea en línea |
| `\int\nolimits_a^b` | Límites al lado aunque sea en display |
| `\substack{i=1 \\ j=2}` | Varias líneas bajo el operador |
| `\sideset{_a^b}{_c^d}\sum` | Índices a los cuatro lados |
| `\mathop{X}` | Convierte `X` en operador (acepta límites) |
| `\operatorname*{arg\,max}_x` | Operador con nombre y límites debajo |

```latex
$$\sum_{\substack{1\le i\le n \\ i \ne j}} a_{ij}$$
```

## Funciones predefinidas

`\sin` `\cos` `\tan` `\cot` `\sec` `\csc` `\arcsin` `\arccos` `\arctan` `\sinh` `\cosh` `\tanh` `\coth` `\exp` `\log` `\ln` `\lg` `\det` `\dim` `\ker` `\deg` `\gcd` `\hom` `\arg` `\Pr` `\min` `\max` `\inf` `\sup` `\lim` `\liminf` `\limsup` `\injlim` `\projlim` `\varinjlim` `\varprojlim` `\varliminf` `\varlimsup`

> Escribir `sin x` produce tres variables en cursiva; `\sin x` da $\sin x$, con la fuente y el espaciado correctos.

## Módulo y operadores propios

| Sintaxis | Resultado |
|---|---|
| `a \bmod n` | $a \bmod n$ (operador binario) |
| `a \equiv b \pmod n` | $a \equiv b \pmod n$ |
| `a \equiv b \mod n` | Sin paréntesis |
| `a \equiv b \pod n` | Solo paréntesis |
| `\operatorname{mcd}(a,b)` | $\operatorname{mcd}(a,b)$ |
| `\DeclareMathOperator{\mcd}{mcd}` | Define `\mcd` reutilizable |
| `\DeclareMathOperator*{\argmax}{arg\,max}` | Con límites debajo |

# 8. Operadores binarios

| Cmd | Símb | Cmd | Símb | Cmd | Símb |
|---|---|---|---|---|---|
| `\pm` | $\pm$ | `\cap` | $\cap$ | `\oplus` | $\oplus$ |
| `\mp` | $\mp$ | `\cup` | $\cup$ | `\ominus` | $\ominus$ |
| `\times` | $\times$ | `\uplus` | $\uplus$ | `\otimes` | $\otimes$ |
| `\div` | $\div$ | `\sqcap` | $\sqcap$ | `\oslash` | $\oslash$ |
| `\cdot` | $\cdot$ | `\sqcup` | $\sqcup$ | `\odot` | $\odot$ |
| `\ast` | $\ast$ | `\vee` | $\vee$ | `\circ` | $\circ$ |
| `\star` | $\star$ | `\wedge` | $\wedge$ | `\bullet` | $\bullet$ |
| `\dagger` | $\dagger$ | `\setminus` | $\setminus$ | `\bigcirc` | $\bigcirc$ |
| `\ddagger` | $\ddagger$ | `\wr` | $\wr$ | `\diamond` | $\diamond$ |
| `\amalg` | $\amalg$ | `\bigtriangleup` | $\bigtriangleup$ | `\bigtriangledown` | $\bigtriangledown$ |
| `\triangleleft` | $\triangleleft$ | `\triangleright` | $\triangleright$ | `\centerdot` | $\centerdot$ |

Extensiones AMS:

| Cmd | Símb | Cmd | Símb | Cmd | Símb |
|---|---|---|---|---|---|
| `\boxplus` | $\boxplus$ | `\ltimes` | $\ltimes$ | `\curlywedge` | $\curlywedge$ |
| `\boxminus` | $\boxminus$ | `\rtimes` | $\rtimes$ | `\curlyvee` | $\curlyvee$ |
| `\boxtimes` | $\boxtimes$ | `\leftthreetimes` | $\leftthreetimes$ | `\barwedge` | $\barwedge$ |
| `\boxdot` | $\boxdot$ | `\rightthreetimes` | $\rightthreetimes$ | `\veebar` | $\veebar$ |
| `\circleddash` | $\circleddash$ | `\intercal` | $\intercal$ | `\doublebarwedge` | $\doublebarwedge$ |
| `\circledast` | $\circledast$ | `\divideontimes` | $\divideontimes$ | `\dotplus` | $\dotplus$ |
| `\circledcirc` | $\circledcirc$ | `\smallsetminus` | $\smallsetminus$ | `\Cap` | $\Cap$ |
| `\Cup` | $\Cup$ | `\unlhd` | $\unlhd$ | `\unrhd` | $\unrhd$ |
| `\lhd` | $\lhd$ | `\rhd` | $\rhd$ | | |

# 9. Relaciones

## Igualdad y semejanza

| Cmd | Símb | Cmd | Símb | Cmd | Símb |
|---|---|---|---|---|---|
| `=` | $=$ | `\equiv` | $\equiv$ | `\doteq` | $\doteq$ |
| `\ne`, `\neq` | $\neq$ | `\sim` | $\sim$ | `\doteqdot` | $\doteqdot$ |
| `\approx` | $\approx$ | `\simeq` | $\simeq$ | `\risingdotseq` | $\risingdotseq$ |
| `\approxeq` | $\approxeq$ | `\cong` | $\cong$ | `\fallingdotseq` | $\fallingdotseq$ |
| `\asymp` | $\asymp$ | `\backsim` | $\backsim$ | `\bumpeq` | $\bumpeq$ |
| `\propto` | $\propto$ | `\backsimeq` | $\backsimeq$ | `\Bumpeq` | $\Bumpeq$ |
| `\varpropto` | $\varpropto$ | `\thicksim` | $\thicksim$ | `\thickapprox` | $\thickapprox$ |

## Orden

| Cmd | Símb | Cmd | Símb | Cmd | Símb |
|---|---|---|---|---|---|
| `\lt` | $<$ | `\leq`, `\le` | $\leq$ | `\ll` | $\ll$ |
| `\gt` | $>$ | `\geq`, `\ge` | $\geq$ | `\gg` | $\gg$ |
| `\lll` | $\lll$ | `\leqq` | $\leqq$ | `\leqslant` | $\leqslant$ |
| `\ggg` | $\ggg$ | `\geqq` | $\geqq$ | `\geqslant` | $\geqslant$ |
| `\lesssim` | $\lesssim$ | `\lessapprox` | $\lessapprox$ | `\eqslantless` | $\eqslantless$ |
| `\gtrsim` | $\gtrsim$ | `\gtrapprox` | $\gtrapprox$ | `\eqslantgtr` | $\eqslantgtr$ |
| `\lessdot` | $\lessdot$ | `\lessgtr` | $\lessgtr$ | `\lesseqgtr` | $\lesseqgtr$ |
| `\gtrdot` | $\gtrdot$ | `\gtrless` | $\gtrless$ | `\gtreqless` | $\gtreqless$ |
| `\prec` | $\prec$ | `\preceq` | $\preceq$ | `\preccurlyeq` | $\preccurlyeq$ |
| `\succ` | $\succ$ | `\succeq` | $\succeq$ | `\succcurlyeq` | $\succcurlyeq$ |
| `\precsim` | $\precsim$ | `\precapprox` | $\precapprox$ | `\curlyeqprec` | $\curlyeqprec$ |
| `\succsim` | $\succsim$ | `\succapprox` | $\succapprox$ | `\curlyeqsucc` | $\curlyeqsucc$ |

> `<` y `>` funcionan directamente, pero dentro de tablas Markdown o cerca de HTML conviene usar `\lt` y `\gt`.

## Conjuntos, pertenencia e inclusión

| Cmd | Símb | Significado |
|---|---|---|
| `\in` | $\in$ | Pertenece |
| `\notin` | $\notin$ | No pertenece |
| `\ni`, `\owns` | $\ni$ | Contiene como elemento |
| `\subset` | $\subset$ | Subconjunto |
| `\supset` | $\supset$ | Superconjunto |
| `\subseteq` | $\subseteq$ | Subconjunto o igual |
| `\supseteq` | $\supseteq$ | Superconjunto o igual |
| `\subseteqq` | $\subseteqq$ | Variante AMS |
| `\supseteqq` | $\supseteqq$ | Variante AMS |
| `\Subset` | $\Subset$ | Inclusión compacta |
| `\Supset` | $\Supset$ | Recíproco |
| `\sqsubset` | $\sqsubset$ | Inclusión cuadrada |
| `\sqsupset` | $\sqsupset$ | Recíproco |
| `\sqsubseteq` | $\sqsubseteq$ | Cuadrada o igual |
| `\sqsupseteq` | $\sqsupseteq$ | Recíproco |
| `\emptyset` | $\emptyset$ | Conjunto vacío |
| `\varnothing` | $\varnothing$ | Vacío (variante) |
| `\complement` | $\complement$ | Complementario |

Conjuntos numéricos: `\mathbb{N}` $\mathbb{N}$, `\mathbb{Z}` $\mathbb{Z}$, `\mathbb{Q}` $\mathbb{Q}$, `\mathbb{R}` $\mathbb{R}$, `\mathbb{C}` $\mathbb{C}$, `\mathbb{F}_q` $\mathbb{F}_q$, `\mathbb{P}` $\mathbb{P}$.

## Lógica y otras relaciones

| Cmd | Símb | Cmd | Símb |
|---|---|---|---|
| `\forall` | $\forall$ | `\exists` | $\exists$ |
| `\nexists` | $\nexists$ | `\neg`, `\lnot` | $\neg$ |
| `\land`, `\wedge` | $\land$ | `\lor`, `\vee` | $\lor$ |
| `\vdash` | $\vdash$ | `\dashv` | $\dashv$ |
| `\models` | $\models$ | `\vDash` | $\vDash$ |
| `\Vdash` | $\Vdash$ | `\Vvdash` | $\Vvdash$ |
| `\therefore` | $\therefore$ | `\because` | $\because$ |
| `\mid` | $\mid$ | `\nmid` | $\nmid$ |
| `\parallel` | $\parallel$ | `\nparallel` | $\nparallel$ |
| `\shortmid` | $\shortmid$ | `\shortparallel` | $\shortparallel$ |
| `\perp` | $\perp$ | `\multimap` | $\multimap$ |
| `\smile` | $\smile$ | `\frown` | $\frown$ |
| `\smallsmile` | $\smallsmile$ | `\smallfrown` | $\smallfrown$ |
| `\bowtie` | $\bowtie$ | `\Join` | $\Join$ |
| `\between` | $\between$ | `\pitchfork` | $\pitchfork$ |
| `\vartriangleleft` | $\vartriangleleft$ | `\vartriangleright` | $\vartriangleright$ |
| `\trianglelefteq` | $\trianglelefteq$ | `\trianglerighteq` | $\trianglerighteq$ |

## Negaciones (AMS)

Formas dedicadas, preferibles a `\not` porque el espaciado es correcto:

`\nless` `\ngtr` `\nleq` `\ngeq` `\nleqslant` `\ngeqslant` `\nleqq` `\ngeqq` `\lneq` `\gneq` `\lneqq` `\gneqq` `\lvertneqq` `\gvertneqq` `\lnsim` `\gnsim` `\lnapprox` `\gnapprox` `\nprec` `\nsucc` `\npreceq` `\nsucceq` `\precnsim` `\succnsim` `\precnapprox` `\succnapprox` `\nsim` `\ncong` `\nshortmid` `\nshortparallel` `\nvdash` `\nvDash` `\nVdash` `\nVDash` `\ntriangleleft` `\ntriangleright` `\ntrianglelefteq` `\ntrianglerighteq` `\nsubseteq` `\nsupseteq` `\nsubseteqq` `\nsupseteqq` `\subsetneq` `\supsetneq` `\subsetneqq` `\supsetneqq` `\varsubsetneq` `\varsupsetneq` `\varsubsetneqq` `\varsupsetneqq`

Genéricos:

| Sintaxis | Efecto |
|---|---|
| `\not=` | Barra superpuesta genérica; el espaciado a veces queda mal |
| `\centernot{\implies}` | Barra centrada (paquete `centernot`), mejor para símbolos anchos |

# 10. Flechas

## Horizontales

| Cmd | Símb | Cmd | Símb |
|---|---|---|---|
| `\leftarrow`, `\gets` | $\leftarrow$ | `\rightarrow`, `\to` | $\rightarrow$ |
| `\Leftarrow` | $\Leftarrow$ | `\Rightarrow` | $\Rightarrow$ |
| `\leftrightarrow` | $\leftrightarrow$ | `\Leftrightarrow` | $\Leftrightarrow$ |
| `\longleftarrow` | $\longleftarrow$ | `\longrightarrow` | $\longrightarrow$ |
| `\Longleftarrow` | $\Longleftarrow$ | `\Longrightarrow` | $\Longrightarrow$ |
| `\longleftrightarrow` | $\longleftrightarrow$ | `\Longleftrightarrow` | $\Longleftrightarrow$ |
| `\implies` | $\implies$ | `\impliedby` | $\impliedby$ |
| `\iff` | $\iff$ | `\mapsto` | $\mapsto$ |
| `\longmapsto` | $\longmapsto$ | `\hookleftarrow` | $\hookleftarrow$ |
| `\hookrightarrow` | $\hookrightarrow$ | `\leadsto` | $\leadsto$ |
| `\rightsquigarrow` | $\rightsquigarrow$ | `\leftrightsquigarrow` | $\leftrightsquigarrow$ |

## Verticales y diagonales

`\uparrow` $\uparrow$ · `\downarrow` $\downarrow$ · `\updownarrow` $\updownarrow$ · `\Uparrow` $\Uparrow$ · `\Downarrow` $\Downarrow$ · `\Updownarrow` $\Updownarrow$ · `\nearrow` $\nearrow$ · `\searrow` $\searrow$ · `\swarrow` $\swarrow$ · `\nwarrow` $\nwarrow$

## Arpones y flechas dobles

| Cmd | Símb | Cmd | Símb |
|---|---|---|---|
| `\leftharpoonup` | $\leftharpoonup$ | `\rightharpoonup` | $\rightharpoonup$ |
| `\leftharpoondown` | $\leftharpoondown$ | `\rightharpoondown` | $\rightharpoondown$ |
| `\upharpoonleft` | $\upharpoonleft$ | `\upharpoonright` | $\upharpoonright$ |
| `\downharpoonleft` | $\downharpoonleft$ | `\downharpoonright` | $\downharpoonright$ |
| `\rightleftharpoons` | $\rightleftharpoons$ | `\leftrightharpoons` | $\leftrightharpoons$ |
| `\leftleftarrows` | $\leftleftarrows$ | `\rightrightarrows` | $\rightrightarrows$ |
| `\leftrightarrows` | $\leftrightarrows$ | `\rightleftarrows` | $\rightleftarrows$ |
| `\upuparrows` | $\upuparrows$ | `\downdownarrows` | $\downdownarrows$ |
| `\Lleftarrow` | $\Lleftarrow$ | `\Rrightarrow` | $\Rrightarrow$ |
| `\twoheadleftarrow` | $\twoheadleftarrow$ | `\twoheadrightarrow` | $\twoheadrightarrow$ |
| `\leftarrowtail` | $\leftarrowtail$ | `\rightarrowtail` | $\rightarrowtail$ |
| `\looparrowleft` | $\looparrowleft$ | `\looparrowright` | $\looparrowright$ |
| `\curvearrowleft` | $\curvearrowleft$ | `\curvearrowright` | $\curvearrowright$ |
| `\circlearrowleft` | $\circlearrowleft$ | `\circlearrowright` | $\circlearrowright$ |
| `\Lsh` | $\Lsh$ | `\Rsh` | $\Rsh$ |
| `\dashleftarrow` | $\dashleftarrow$ | `\dashrightarrow` | $\dashrightarrow$ |

Negadas: `\nleftarrow` $\nleftarrow$ · `\nrightarrow` $\nrightarrow$ · `\nLeftarrow` $\nLeftarrow$ · `\nRightarrow` $\nRightarrow$ · `\nleftrightarrow` $\nleftrightarrow$ · `\nLeftrightarrow` $\nLeftrightarrow$

## Flechas extensibles con etiqueta

Crecen para acomodar el texto. Argumento obligatorio `{arriba}`, opcional `[abajo]`.

| Comando | Nota |
|---|---|
| `\xrightarrow{f}` | $\xrightarrow{f}$ |
| `\xleftarrow[g]{f}` | Etiqueta arriba y abajo |
| `\xleftrightarrow{f}` | Bidireccional |
| `\xRightarrow{f}`, `\xLeftarrow{f}`, `\xLeftrightarrow{f}` | Dobles |
| `\xhookleftarrow{f}`, `\xhookrightarrow{f}` | Con gancho |
| `\xmapsto{f}` | Aplicación |
| `\xtwoheadleftarrow{f}`, `\xtwoheadrightarrow{f}` | Doble punta |
| `\xrightharpoonup{f}`, `\xrightharpoondown{f}` | Arpones derecha |
| `\xleftharpoonup{f}`, `\xleftharpoondown{f}` | Arpones izquierda |
| `\xrightleftharpoons{f}`, `\xleftrightharpoons{f}` | Equilibrio |
| `\xtofrom{f}` | Ida y vuelta |
| `\Newextarrow{\xUp}{5,5}{0x2191}` | Define una flecha extensible propia |

```latex
$$A \xrightarrow[\text{iso}]{\ \varphi\ } B$$
```

# 11. Delimitadores

| Cmd | Símb | Cmd | Símb |
|---|---|---|---|
| `(` `)` | $()$ | `\langle` `\rangle` | $\langle\rangle$ |
| `[` `]`, `\lbrack` `\rbrack` | $[]$ | `\lvert` `\rvert` | $\lvert\rvert$ |
| `\{` `\}`, `\lbrace` `\rbrace` | $\{\}$ | `\lVert` `\rVert` | $\lVert\rVert$ |
| `\lfloor` `\rfloor` | $\lfloor\rfloor$ | `\vert` | $\vert$ |
| `\lceil` `\rceil` | $\lceil\rceil$ | `\Vert` | $\Vert$ |
| `\ulcorner` `\urcorner` | $\ulcorner\urcorner$ | `\llcorner` `\lrcorner` | $\llcorner\lrcorner$ |
| `\lgroup` `\rgroup` | Paréntesis alargados | `\lmoustache` `\rmoustache` | Bigotes |
| `\arrowvert` `\Arrowvert` | Barras extensibles | `\bracevert` | Barra de llave |
| `\backslash` | $\backslash$ | `\uparrow`, `\Downarrow`… | Válidas como delimitador |

## Escalado

| Sintaxis | Efecto |
|---|---|
| `\left( ... \right)` | Escala automática al contenido |
| `\left. ... \right)` | Lado izquierdo invisible |
| `\left( ... \middle\vert ... \right)` | Separador interno que también escala |
| `\big(` `\Big(` `\bigg(` `\Bigg(` | Cuatro tamaños fijos |
| `\bigl` `\bigr` `\bigm` | Variantes con espaciado de apertura, cierre y relación |
| `\Bigl` `\Bigr` `\Bigm`, `\biggl`…, `\Biggl`… | Lo mismo para los otros tamaños |

> [!warning] `\left` y `\right` van siempre en pareja
> Un `\left(` sin su `\right` aborta la fórmula entera. Si solo necesitas un lado, cierra con `\right.`.

## Delimitadores emparejados (`mathtools`)

```latex
$$\DeclarePairedDelimiter\abs{\lvert}{\rvert}
\abs{x} \quad \abs*{\frac{a}{b}}$$
```

- `\DeclarePairedDelimiter\cmd{izq}{der}` — la versión estrella `\cmd*{}` escala automáticamente.
- `\DeclarePairedDelimiterX` y `\DeclarePairedDelimiterXPP` para varios argumentos.

# 12. Acentos y decoraciones

## Acentos de un carácter.

| Cmd | Ej. | Cmd | Ej. |
|---|---|---|---|
| `\hat{x}` | $\hat{x}$ | `\check{x}` | $\check{x}$ |
| `\tilde{x}` | $\tilde{x}$ | `\breve{x}` | $\breve{x}$ |
| `\bar{x}` | $\bar{x}$ | `\acute{x}` | $\acute{x}$ |
| `\vec{x}` | $\vec{x}$ | `\grave{x}` | $\grave{x}$ |
| `\dot{x}` | $\dot{x}$ | `\mathring{x}` | $\mathring{x}$ |
| `\ddot{x}` | $\ddot{x}$ | `\dddot{x}` | $\dddot{x}$ |
| `\ddddot{x}` | $\ddddot{x}$ | | |

## Decoraciones extensibles

| Cmd | Uso |
|---|---|
| `\widehat{ABC}` | Sombrero ancho |
| `\widetilde{ABC}` | Tilde ancha |
| `\overline{ABC}` | Barra superior |
| `\underline{ABC}` | Barra inferior |
| `\overbrace{a+b}^{n}` | Llave superior con etiqueta |
| `\underbrace{a+b}_{n}` | Llave inferior con etiqueta |
| `\overbracket{a+b}` | Corchete superior (mathtools) |
| `\underbracket{a+b}` | Corchete inferior (mathtools) |
| `\overrightarrow{AB}` | Flecha superior |
| `\overleftarrow{AB}` | Flecha superior a izquierda |
| `\overleftrightarrow{AB}` | Doble |
| `\underrightarrow{AB}` | Flecha inferior |
| `\underleftarrow{AB}`, `\underleftrightarrow{AB}` | Variantes inferiores |
| `\overset{def}{=}` | Superponer arriba |
| `\underset{n\to\infty}{\lim}` | Superponer abajo |
| `\stackrel{f}{\to}` | Etiqueta sobre una relación |
| `\buildrel f \over \to` | Forma TeX equivalente |

```latex
$$\underbrace{1+2+\cdots+n}_{n\ \text{sumandos}} = \frac{n(n+1)}{2}$$
```

## Tachado, cajas y realce

| Cmd | Efecto |
|---|---|
| `\cancel{x}` | Tachado diagonal ascendente |
| `\bcancel{x}` | Tachado diagonal descendente |
| `\xcancel{x}` | Aspa |
| `\cancelto{0}{x}` | Tachado con destino |
| `\boxed{E=mc^2}` | Caja matemática |
| `\fbox{texto}` | Caja de texto |
| `\enclose{circle}{x}` | Notación arbitraria (ver abajo) |
| `\bbox[yellow]{x}` | Caja con fondo |
| `\bbox[5px, border: 2px solid red]{x}` | Caja con CSS |

Notaciones válidas de `\enclose`, combinables con comas: `longdiv`, `actuarial`, `radical`, `box`, `roundedbox`, `circle`, `left`, `right`, `top`, `bottom`, `updiagonalstrike`, `downdiagonalstrike`, `verticalstrike`, `horizontalstrike`, `madruwb`, `updiagonalarrow`, `northeastarrow`.

```latex
$$\enclose{circle}[mathcolor="red"]{x} \quad
\enclose{horizontalstrike,box}{y}$$
```

## Color

| Sintaxis | Efecto |
|---|---|
| `\color{red} x + y` | Conmutador: colorea hasta el fin del grupo |
| `\textcolor{red}{x} + y` | Colorea solo el argumento (recomendado) |
| `\color{#1a73e8}` | Acepta cualquier color CSS, incluido hexadecimal |
| `\color[rgb]{1,0,0}` | Modelo `rgb` (0–1) |
| `\color[RGB]{255,0,0}` | Modelo `RGB` (0–255) |
| `\color[gray]{0.5}` | Escala de grises |
| `\definecolor{mio}{RGB}{20,110,180}` | Define un color reutilizable |
| `\colorbox{yellow}{$x$}` | Fondo coloreado |
| `\fcolorbox{red}{yellow}{$x$}` | Marco y fondo |

> [!warning] Colores y tema oscuro
> Un `\textcolor{black}{...}` desaparece en tema oscuro. Para notas que se leen en ambos temas, usa colores de contraste medio (rojo, azul, verde saturados) o aplica CSS con `\class{...}{...}` y define la regla en un snippet.

# 13. Símbolos misceláneos

| Cmd | Símb | Cmd | Símb | Cmd | Símb |
|---|---|---|---|---|---|
| `\infty` | $\infty$ | `\partial` | $\partial$ | `\nabla` | $\nabla$ |
| `\prime` | $\prime$ | `\backprime` | $\backprime$ | `\surd` | $\surd$ |
| `\angle` | $\angle$ | `\measuredangle` | $\measuredangle$ | `\sphericalangle` | $\sphericalangle$ |
| `\triangle` | $\triangle$ | `\blacktriangle` | $\blacktriangle$ | `\triangledown` | $\triangledown$ |
| `\blacktriangledown` | $\blacktriangledown$ | `\square` | $\square$ | `\blacksquare` | $\blacksquare$ |
| `\lozenge` | $\lozenge$ | `\blacklozenge` | $\blacklozenge$ | `\bigstar` | $\bigstar$ |
| `\circledS` | $\circledS$ | `\top` | $\top$ | `\bot` | $\bot$ |
| `\flat` | $\flat$ | `\natural` | $\natural$ | `\sharp` | $\sharp$ |
| `\clubsuit` | $\clubsuit$ | `\diamondsuit` | $\diamondsuit$ | `\heartsuit` | $\heartsuit$ |
| `\spadesuit` | $\spadesuit$ | `\checkmark` | $\checkmark$ | `\maltese` | $\maltese$ |
| `\yen` | $\yen$ | `\diagup` | $\diagup$ | `\diagdown` | $\diagdown$ |
| `\S` | $\S$ | `\P` | $\P$ | `\#` | $\#$ |

## Puntos suspensivos

| Cmd | Posición | Uso |
|---|---|---|
| `\ldots` | Línea base | Listas: $a_1,\ldots,a_n$ |
| `\cdots` | Centrados | Sumas: $a_1+\cdots+a_n$ |
| `\vdots` | Verticales | Matrices |
| `\ddots` | Diagonales | Matrices |
| `\dots` | Automático | Elige según el contexto |
| `\dotsc` | Con comas | Semántico |
| `\dotsb` | Con operadores binarios | Semántico |
| `\dotsm` | Multiplicación | Semántico |
| `\dotsi` | Integrales | Semántico |
| `\dotso` | Otros | Semántico |

## Símbolos de texto (`textcomp`, `gensymb`)

`\degree` · `\celsius` · `\ohm` · `\micro` · `\perthousand` · `\textdegree` · `\textcelsius` · `\textohm` · `\textmu` · `\textcopyright` · `\textregistered` · `\textbullet` · `\textbar` · `\textvisiblespace` · `\textquotedblleft` · `\textcompwordmark`

```latex
$25\,\degree\mathrm{C} \qquad 3{,}3\ \mathrm{k}\ohm$
```

## Caracteres Unicode arbitrarios

```latex
$\unicode{x2721}$              ← por punto de código
$\unicode[.55,0.05]{x22D6}$    ← con ajuste de altura y profundidad
```

# 14. Matrices y arrays

## Entornos de matriz

| Entorno | Delimitador |
|---|---|
| `matrix` | Ninguno |
| `pmatrix` | Paréntesis |
| `bmatrix` | Corchetes |
| `Bmatrix` | Llaves |
| `vmatrix` | Barras simples (determinante) |
| `Vmatrix` | Barras dobles (norma) |
| `smallmatrix` | Sin delimitador, tamaño reducido para uso en línea |

Cada uno tiene variante estrella (`pmatrix*`, `bmatrix*`…, de `mathtools`) que acepta alineación opcional: `\begin{bmatrix*}[r] ... \end{bmatrix*}`.

```latex
$$A=\begin{pmatrix} a_{11} & \cdots & a_{1n} \\ \vdots & \ddots & \vdots \\ a_{m1} & \cdots & a_{mn}\end{pmatrix}$$
```

- `&` separa columnas, `\\` separa filas.
- `\\[6pt]` añade separación vertical extra a esa fila.

## `array`: control de columnas

```latex
$$\begin{array}{l|cr}
 izq & centro & der \\ \hline
 1 & 2 & 3
\end{array}$$
```

| Especificador | Efecto |
|---|---|
| `l`, `c`, `r` | Alineación de columna |
| Barra vertical | Línea vertical entre columnas |
| `:` | Línea vertical discontinua |
| `\hline` | Línea horizontal |
| `\hdashline` | Línea horizontal discontinua |

Coloreado (`colortbl`): `\rowcolor{gray}`, `\columncolor{yellow}`, `\cellcolor{red}`.

# 15. Ecuaciones multilínea

| Entorno | Para qué sirve |
|---|---|
| `aligned` | Alinear por `&` dentro de un `$$…$$` |
| `align`, `align*` | Alineación como bloque independiente |
| `alignat`, `alignedat` | Alineación con número explícito de columnas |
| `gather`, `gathered` | Centrar cada línea sin alinear |
| `split` | Partir una ecuación en varias líneas dentro de otro entorno |
| `multline` | Primera línea a la izquierda, última a la derecha |
| `multlined` | Versión de mathtools usable dentro de otras estructuras |
| `eqnarray` | Heredado; evítalo, el espaciado es incorrecto |
| `displaylines`, `eqalign` | Formas TeX planas |
| `cases` | Definición por casos |
| `dcases` | `cases` en `\displaystyle` (mathtools) |
| `rcases` | Llave a la derecha (mathtools) |
| `drcases` | Combinación de las dos anteriores |
| `subarray` | Subíndices multilínea bajo operadores |
| `CD` | Diagramas conmutativos (amscd) |

```latex
$$\begin{aligned}
(a+b)^2 &= a^2 + 2ab + b^2 \\
        &= a^2 + b^2 + 2ab
\end{aligned}$$
```

```latex
$$f(x)=\begin{cases}
 x^2 & \text{si } x \ge 0 \\
 -x  & \text{en otro caso}
\end{cases}$$
```

Utilidades de alineación (`mathtools`): `\MoveEqLeft`, `\shoveleft`, `\shoveright`, `\ArrowBetweenLines`, `\vdotswithin{=}`, `\shortvdotswithin{=}`, `\adjustlimits`.

## Diagramas conmutativos (`amscd`)

```latex
$$\begin{CD}
A @>f>> B \\
@VgVV @VVhV \\
C @>>k> D
\end{CD}$$
```

`@>>>` flecha derecha · `@<<<` izquierda · `@VVV` abajo · `@AAA` arriba · `@=` igualdad · `@.` hueco. Las etiquetas van entre los símbolos de dirección.

## Árboles de demostración (`bussproofs`)

```latex
$$\begin{prooftree}
\AxiomC{$A$}
\AxiomC{$A \to B$}
\BinaryInfC{$B$}
\end{prooftree}$$
```

Comandos: `\AxiomC`, `\UnaryInfC`, `\BinaryInfC`, `\TrinaryInfC`, `\QuaternaryInfC`, `\QuinaryInfC`, `\RightLabel`, `\LeftLabel`, y las abreviaturas `\AXC`, `\UIC`, `\BIC`, `\TIC`.

# 16. Numeración y etiquetas

> [!warning] Obsidian no numera ecuaciones
> MathJax arranca con `tags: 'none'` y Obsidian no lo cambia: `align`/`equation` **no** generan números y `\ref`/`\eqref` no resuelven a nada útil. Solo la numeración manual es fiable.

| Comando | Efecto |
|---|---|
| `\tag{3.1}` | Etiqueta manual a la derecha, entre paréntesis |
| `\tag*{...}` | Etiqueta manual sin paréntesis |
| `\notag`, `\nonumber` | Suprime la etiqueta de esa línea |
| `\label{eq:x}` | Se acepta, pero sin numeración no sirve de ancla |
| `\newtagform{corch}{[}{]}` | Define formato de etiqueta (`tagformat` / `mathtools`) |
| `\usetagform{corch}` | Aplica el formato definido |

```latex
$$e^{i\pi} + 1 = 0 \tag{Euler}$$
```

Para numeración automática y referencias cruzadas reales hace falta un plugin de la comunidad; con MathJax puro en Obsidian no es posible.

# 17. Macros propias

| Comando | Sintaxis |
|---|---|
| `\newcommand` | `\newcommand{\R}{\mathbb{R}}` |
| Con argumentos | `\newcommand{\norm}[1]{\left\lVert #1 \right\rVert}` |
| Con argumento opcional | `\newcommand{\abs}[2][]{#1\lvert #2 #1\rvert}` |
| `\renewcommand` | Redefine una macro existente |
| `\def` | `\def\R{\mathbb{R}}` (primitiva TeX, sin comprobaciones) |
| `\let` | `\let\oldphi\phi` (alias) |
| `\DeclareMathOperator` | `\DeclareMathOperator{\rank}{rank}` |
| `\newenvironment` | `\newenvironment{sis}{\left\{\begin{aligned}}{\end{aligned}\right.}` |
| `\renewenvironment` | Redefine un entorno |

> `\providecommand` **no** existe en este motor.

## Preámbulo por nota

Las definiciones persisten en la instancia de MathJax mientras la app siga abierta, lo que produce dos efectos indeseados: al abrir la nota en frío las macros solo existen a partir del bloque donde se definen, y pueden filtrarse a otras notas de la misma sesión. Patrón robusto: un bloque casi invisible al principio de la nota.

```latex
$$
\newcommand{\R}{\mathbb{R}}
\newcommand{\norm}[1]{\left\lVert #1\right\rVert}
\DeclareMathOperator{\rank}{rank}
$$
```

Ese bloque renderiza vacío y deja las macros listas para el resto de la nota. Para una biblioteca de macros compartida por todo el vault hace falta un plugin que inyecte un preámbulo global: MathJax en Obsidian no lo ofrece de serie.

# 18. Química: `mhchem`

Cargado por defecto. `\ce{}` interpreta notación química, no TeX.

| Sintaxis | Resultado |
|---|---|
| `\ce{H2O}` | Subíndices automáticos |
| `\ce{SO4^2-}` | Cargas |
| `\ce{^{227}_{90}Th+}` | Isótopos |
| `\ce{2H2 + O2 -> 2H2O}` | Reacción |
| `\ce{A <=> B}` | Equilibrio |
| `\ce{A <=>> B}`, `\ce{A <<=> B}` | Equilibrio desplazado |
| `\ce{A ->[cat] B}` | Condiciones sobre la flecha |
| `\ce{A ->[arriba][abajo] B}` | Arriba y abajo |
| `\ce{CaCO3 ->[\Delta] CaO + CO2 ^}` | `^` gas, `v` precipitado |
| `\ce{$x$ H2O}` | Volver a modo matemático |
| `\ce{A\cdot B}` | Aductos |
| `\pu{123 kJ//mol}` | Unidades físicas formateadas |

```latex
$$\ce{Zn^2+ + 2OH- -> Zn(OH)2 v}$$
```

# 19. Notación de Dirac: `braket`

Cargado por defecto.

| Sintaxis | Uso |
|---|---|
| `\bra{\psi}` | Bra |
| `\ket{\phi}` | Ket |
| `\braket{\psi\vert\phi}` | Producto interno |
| `\Bra{\psi}`, `\Ket{\phi}`, `\Braket{...}` | Versiones que escalan |
| `\set{x \vert x>0}` | Conjunto |
| `\Set{...}` | Conjunto con delimitadores escalados |
| `\ketbra{\psi}{\phi}` | Proyector |

Con `\require{physics}` se añaden `\dv`, `\pdv`, `\derivative`, `\qty`, `\pqty`, `\abs`, `\norm`, `\eval`, `\grad`, `\div`, `\curl`, `\laplacian`, `\comm`, `\anticommutator`, `\poissonbracket`, `\tr`, `\Tr`, `\rank`, `\Res`, `\PV`, `\outerproduct`.

# 20. Extras: HTML, interacción y utilidades

| Paquete | Comando | Uso |
|---|---|---|
| `html` | `\href{https://…}{x}` | Enlace desde una fórmula |
| `html` | `\class{mi-clase}{x}` | Añade clase CSS (para snippets) |
| `html` | `\style{color:teal}{x}` | CSS en línea |
| `html` | `\cssId{eq1}{x}` | Identificador HTML |
| `action` | `\mathtip{x}{explicación}` | Tooltip matemático al pasar el ratón |
| `action` | `\texttip{x}{texto}` | Tooltip de texto |
| `action` | `\toggle{a}{b}\endtoggle` | Alterna contenido al hacer clic |
| `verb` | `\verb+código+` | Texto literal monoespaciado (delimitador libre) |
| `empheq` | `\Aboxed{a &= b}` | Caja que respeta la alineación |
| `mathtools` | `\prescript{a}{b}{C}` | Pre-índices |
| `mathtools` | `\coloneqq`, `\colonapprox`, `\dblcolon` | Familia de dos puntos |
| `noundefined` | — | Muestra en rojo los comandos desconocidos en vez de romper el render |

> `\class` combinado con un snippet CSS es la forma limpia de resaltar términos sin romper el tema oscuro: `$\class{destacado}{x^2}$` más `.destacado { background: rgba(255,200,0,.25); }`.

# 21. Comandos no soportados

Verificado contra el motor que Obsidian carga. Estos comandos, habituales en LaTeX o en KaTeX, **no existen** y provocan «Undefined control sequence»:

| Comando | Alternativa en Obsidian |
|---|---|
| `\bm` | `\boldsymbol` o `\pmb` |
| `\sout` | `\enclose{horizontalstrike}{...}` |
| `\oiint`, `\oiiint` | `\unicode{x222F}`, `\unicode{x2230}` |
| `\iddots` | `\unicode{x22F0}` |
| `\intertext`, `\shortintertext` | Cerrar el bloque, escribir el texto y abrir otro |
| `\newtheorem` | No hay entornos de teorema; usar callouts de Obsidian |
| `\providecommand` | `\newcommand` |
| `\medspace`, `\thickspace` | `\:` y `\;` |
| `\bordermatrix` | `array` con `\hline` y etiquetas manuales |
| `\cline`, `\toprule`, `\midrule` | `\hline`, `\hdashline` |
| `\numberwithin` | Numeración manual con `\tag{}` |
| `\copyright`, `\pounds`, `\dag`, `\ddag` | `\textcopyright`, `\dagger`, `\ddagger` |
| `\usepackage`, `\documentclass`, `\includegraphics` | No aplican: no hay documento LaTeX |
| `\begin{tabular}`, `\multicolumn` | `array` o tablas Markdown nativas |
| `\label` + `\ref` funcionales | No hay numeración; usar enlaces internos de Obsidian |
| `\widecheck`, `\overgroup`, `\undergroup` | `\check`, `\overbrace`, `\underbrace` |

> [!note] MathJax ≠ KaTeX
> Muchas cheatsheets de internet documentan KaTeX (el motor de Quartz, HackMD o Notion). Los conjuntos de comandos se solapan pero no coinciden. Detalle relevante: Obsidian **sí** incluye KaTeX, pero solo dentro de Mermaid; las matemáticas en diagramas Mermaid siguen las reglas de KaTeX, no las de esta nota.

# 22. Errores comunes y depuración

| Síntoma | Causa | Solución |
|---|---|---|
| Se ve el `$` y el código en crudo | Espacio pegado al delimitador | `$x$`, no `$ x $` |
| Fórmula en línea ignorada | Dígito justo tras el `$` de cierre | Añadir un espacio: `$x$ 2` |
| Toda la fórmula en rojo | Comando inexistente | Ver la sección 21 |
| «Misplaced &» | `&` fuera de un entorno alineado | Envolver en `aligned` o `array` |
| «Missing \right» | `\left` sin pareja | Cerrar con `\right.` si hace falta |
| Salto de línea ignorado | `\\` fuera de un entorno | Usar `aligned`, `gather` o `cases` |
| La fórmula se corta en `%` | `%` inicia comentario | Escapar `\%` |
| El texto siguiente sale en cursiva o roto | `_` o `*` sueltos interpretados por Markdown | Mantenerlos dentro de `$…$` o escapar |
| Fórmula rota dentro de una tabla | La barra vertical parte la celda | Usar `\mid`, `\vert`, `\Vert` |
| Se ve bien en edición y mal en lectura | Live Preview y modo lectura son pasadas distintas | Comprobar siempre en modo lectura |
| Nada renderiza tras editar plantillas | La instancia de MathJax quedó con macros corruptas | `Ctrl+P` → *Reload app without saving* |

## Diagnóstico sistemático

1. **Aislar**: copia la fórmula sola en una nota nueva. Si funciona, el problema es el contexto (tabla, callout, lista), no la fórmula.
2. **Bisecar**: elimina la mitad del contenido hasta que renderice; el comando culpable está en la mitad eliminada.
3. **Comprobar el comando**: pruébalo aislado en un `$$…$$`. El paquete `noundefined` lo pinta en rojo si no existe.
4. **Comprobar el delimitador**: sustituye `$…$` por `$$…$$`. Si así renderiza, el problema es una de las reglas de la sección 2.
5. **Reiniciar en frío**: si la nota usa macros, recarga la app; las definiciones persistentes pueden estar enmascarando un error.

# 23. Integración con Obsidian

## Matemáticas en cada contexto

| Contexto | ¿Funciona? | Detalle |
|---|---|---|
| Párrafo | Sí | Con las reglas de la sección 2 |
| Tabla Markdown | Sí | Evitar la barra vertical literal dentro de la fórmula |
| Callout `> [!note]` | Sí | Cada línea debe empezar por `>`, incluidas las del `$$` |
| Lista | Sí | Indentar el `$$` al nivel del elemento |
| Cita `>` | Sí | Igual que en callouts |
| Encabezado | Sí, pero desaconsejado | El enlace `[[Nota#Encabezado]]` debe contener el texto crudo con `$` |
| Bloque de código | No | Se muestra literal, que es lo deseable para documentar sintaxis |
| Nombre de fichero | No | El renderizado solo ocurre en el cuerpo |
| Canvas | Sí | Las tarjetas de texto usan el mismo renderizador |

Bloque display dentro de un callout:

```markdown
> [!note] Definición de límite
> $$\forall \varepsilon>0\ \exists \delta>0 : \lvert x-a\rvert<\delta \implies \lvert f(x)-f(a)\rvert<\varepsilon$$
```

## Exportación a PDF

`better-export-pdf` usa el mismo renderizado CHTML, así que lo que se ve en modo lectura es lo que se imprime. Con la configuración A4 y margen 0 del vault conviene vigilar:

- Los `$$…$$` muy anchos (matrices grandes, `aligned` largos) se recortan por el borde. Repártelos en varias líneas o redúcelos con `\small`.
- CHTML depende de las fuentes MathJax embebidas: no sustituyas la familia tipográfica de `mjx-container` en tus snippets CSS o el espaciado se descuadra.
- Fuerza los saltos de página con `break-page` antes de un bloque grande, nunca en medio.

## Rendimiento

Cada bloque matemático es una compilación TeX independiente. En notas con centenares de fórmulas la apertura se ralentiza de forma perceptible.

- Prefiere un `$$…$$` con `aligned` a diez `$$…$$` consecutivos: una compilación en lugar de diez.
- Las macros no aceleran el render, pero reducen el texto a parsear y evitan errores repetidos.
- `\enclose`, `\bbox` y `\class` generan HTML adicional; en tablas largas se nota.
- Evita fórmulas en encabezados: se recompilan también en el esquema y en la búsqueda.

# 24. Recetas rápidas

```latex
% Límite y derivada
$$f'(a)=\lim_{h\to 0}\frac{f(a+h)-f(a)}{h}$$

% Sistema de ecuaciones con llave
$$\left\{\begin{aligned}
2x + y &= 5 \\
x - 3y &= -1
\end{aligned}\right.$$

% Integral definida con diferencial correcta
$$\int_{0}^{\pi} \sin x\,\mathrm{d}x = 2$$

% Serie
$$\sum_{n=1}^{\infty}\frac{1}{n^2}=\frac{\pi^2}{6}$$

% Matriz aumentada
$$\left[\begin{array}{cc|c}
1 & 2 & 3 \\
4 & 5 & 6
\end{array}\right]$$

% Función a trozos
$$\operatorname{sgn}(x)=\begin{cases}
 1 & x>0\\ 0 & x=0\\ -1 & x<0
\end{cases}$$

% Probabilidad condicionada con barras que escalan
$$P\left(A \middle\vert B\right)=\frac{P(A\cap B)}{P(B)}$$

% Notación asintótica
$$T(n)=\Theta(n\log n),\qquad T(n)\in O(n^2)$$

% Criptografía: RSA y función de Euler
$$c \equiv m^{e} \pmod{n}, \qquad \varphi(n)=(p-1)(q-1)$$

% Entropía de Shannon
$$H(X)=-\sum_{i=1}^{n} p_i \log_2 p_i$$

% Reacción química
$$\ce{2Fe + 3Cl2 ->[\Delta] 2FeCl3}$$

% Diagrama conmutativo
$$\begin{CD} A @>f>> B \\ @VgVV @VVhV \\ C @>>k> D \end{CD}$$
```

# Recursos
### [MathJax 3 — TeX/LaTeX Support](https://docs.mathjax.org/en/latest/input/tex/index.html)
### [MathJax — Extensiones TeX](https://docs.mathjax.org/en/latest/input/tex/extensions.html)
### [MathJax — Índice de comandos soportados](https://docs.mathjax.org/en/latest/input/tex/macros/index.html)
### [mhchem — Manual de notación química](https://mhchem.github.io/MathJax-mhchem/)
### [Obsidian Help — Advanced formatting syntax](https://help.obsidian.md/Editing+and+formatting/Advanced+formatting+syntax)
### [Detexify — dibuja un símbolo y obtén su comando](https://detexify.kirelabs.org/classify.html)
### [The Comprehensive LaTeX Symbol List](https://ctan.org/pkg/comprehensive)
### [LaTeX/Mathematics — Wikibooks](https://en.wikibooks.org/wiki/LaTeX/Mathematics)

Relacionado: [[RegEx]] · [[control_chars]]
