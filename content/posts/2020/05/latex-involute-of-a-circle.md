---
title: "Latex Involute of a Circle"
date: 2020-05-24T10:16:08+03:00
lastmod: 2020-05-24T10:16:08+03:00
draft: true

categories: ["Proiecte"]
tags: ["latex", "geometry", "mathematics", "trigonometry"]
---

<figure>
    <video controls autoplay style="width: 100%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo8.mp4" type="video/mp4">
    </video>
    <figcaption>Demonstrare grafică cum evolventa funcționează.</figcaption>
</figure>

Codul complet al evolventei îl găsiți mai jos sau pe [repository-ul Github](https://github.com/sunt-programator/latex-workpapers/blob/master/involute-of-circle/involute-demo.tex). În continuare eu voi explica mai detailat fiecare secțiune de cod ce face.

``` latex
\documentclass[tikz,border=10pt]{standalone}

\usepackage{pgfplots,amsmath}
\pgfplotsset{compat=newest}

\begin{document}
\pgfmathsetmacro\radius{2}

% Colors
\definecolor{tangentLineColor}{HTML}{BC5090}
\definecolor{remainingArcColor}{HTML}{003F5C}
\definecolor{involuteSplineColor}{HTML}{58508D}
\definecolor{accentColor}{HTML}{FF6361}
\colorlet{dashedLineColor}{black}

% Styles
\tikzstyle{information text}=[fill=red!10,inner sep=1ex]
\pgfplotsset{
  /pgf/number format/textnumber/.style={
      fixed,
      fixed zerofill,
      precision=2,
      1000 sep={,},
    },
}

\foreach \rollAngle in {0.05,0.1,...,3.25}
  {
    \begin{tikzpicture}
      [
        point/.style = {draw, circle, fill = black, inner sep = 1pt},
        dot/.style   = {draw, circle, fill = black, inner sep = .2pt},
        declare function = {
            involutex(\radius,\psi) = \radius * (cos(\psi) + \psi * sin(\psi));
            involutey(\radius,\psi) = \radius * (sin(\psi) - \psi * cos(\psi));
            arcx(\radius,\a,\psi) = \a + \radius * cos(\psi);
            arcy(\radius,\b,\psi) = \b + \radius * sin(\psi);
          },
      ]

      \pgfmathsetmacro\rollAngleDeg{deg(\rollAngle)}
      \pgfmathsetmacro\arcLength{0.5 * \rollAngle * \radius^2}
      \pgfmathsetmacro\curvature{1 / (\radius * \rollAngle)}

      \begin{axis}[
          name=plotAxis,
          trig format=rad,
          axis equal,
          axis lines=center,
          grid=both,
          xlabel=$x$,
          ylabel=$y$,
          xmin=-5,xmax=5,
          ymin=-3,ymax=7,
          xticklabels=\empty,
          yticklabels=\empty,
        ]

        \coordinate (O) at (0,0);
        \coordinate (Or) at (\radius,0);
        \coordinate (L1) at ({arcx(\radius,0,\rollAngle)},{arcy(\radius,0,\rollAngle)});
        \coordinate (L2) at ({involutex(\radius,\rollAngle)},{involutey(\radius,\rollAngle)});

        \addplot [domain=2*pi:\rollAngle,samples=200,remainingArcColor,thick,line cap=round]({arcx(\radius,0,x)},{arcy(\radius,0,x)});
        \addplot [domain=0:\rollAngle,samples=200,dashedLineColor,dashed,line cap=round]({arcx(\radius,0,x)},{arcy(\radius,0,x)});
        \addplot [domain=0.01:\rollAngle,samples=200,involuteSplineColor,thick,line cap=round]({involutex(\radius,x)},{involutey(\radius,x)});
        \draw[tangentLineColor,thick] (L1) -- (L2);

        \draw[dashedLineColor,dashed] (O) -- (L1) node [accentColor,pos=0.5,sloped,above] {$r$};
        \addplot [domain=0:\rollAngle,samples=200,accentColor,line cap=round]({arcx(.4,0,x)},{arcy(.4,0,x)}) node[] at (.5, -.3) {$\psi$};


      \end{axis}

      \node [xshift=.5cm,below right,align=center,text width=6cm,style=information text] at (plotAxis.north east)
      {
        This is a demonstration how the {\color{accentColor} involute of a circle} works.
        So, {\color{accentColor} $r$} is radius of the circle, {\color{accentColor} $\psi$} --- roll angle,
        {\color{accentColor} $L$} --- arc length and {\color{accentColor} $k$} -- curvature of
        the involute.
        \begin{align*}
          {\color{accentColor} r}      & = const                                                  &   & = \radius                                                    \\
          {\color{accentColor} \psi}   & = \pgfmathprintnumber[textnumber]{\rollAngle}\text{ rad} &   & \approx \pgfmathprintnumber[textnumber]{\rollAngleDeg}^\circ \\
          {\color{accentColor} L}      & = \frac{1}{2} \psi r^2                                   &   & = \pgfmathprintnumber[textnumber]{\arcLength}                \\
          {\color{accentColor} \kappa} & = \frac{1}{\psi r}                                       &   & \approx \pgfmathprintnumber[textnumber]{\curvature}
        \end{align*}
      };

    \end{tikzpicture}
  }
\end{document}
```

## Structura de bază a documentului LaTeX și preambula ei

Când $ \LaTeX $ procesează un document, el se așteaptă ca documentul să conțină o anumită structură. Astfel, fiecare document trebuie să conțină comenzile:

```latex
\documentclass{...}
\begin{document}
  ...
\end{document}
```

Între comenzile `\documentclass` și `\begin{document}` se afla așa numita preambulă. În secțiunea dată se conțin comenzile care vor afecta întrecul document LaTeX. Tot aici se importează pachetele necesare și se fac careva setări asupra lor.

În cazul nostru, comanda `\documentclass` mai conține careva opțiuni, izolate între paranteze patrate și specifică ce tip de clasă se va folosi pentru document, aceasta fiind izolată între acolade.

```latex
\documentclass[tikz,border=10pt]{standalone}
```

### Clasa și pachetul Standalone

Clasa `standalone` este proiectată pentru a crea fragmente individuale de conținut. Această clasă este utilă la generarea imaginilor care vor fi incluse în alte documente [^standalone].

Pachetul `standalone` permite utilizatorilor să plaseze cu ușurință imagini sau alt material în fișierele proprii și să le compileze de sine-stătător sau ca parte a unui document principal [^standalone-package-1].

### Opțiunea și pachetul TikZ

Pachetul TikZ este probabil cel mai complex și puternic instrument de a crea elemente grafice în LaTeX. Cu acest pachet putem crea elemente grafice complexe folosind așa elemente simple ca linii, puncte, curbe, cercuri, dreptunghiuri, etc.

Pentru imaginile desenate cu TikZ este oferită o opțiune dedicată `tikz` care încarcă acest pachet și configurează mediul tikzpicture pentru a crea o singură pagină decupată [^standalone-package-8].

### Opțiunea border

Opțiunea `border=10pt` specifică că documentul va avea un chenar de 10pt sau, altfel spus, va avea o margine din toate părțile de 10pt.

### Importarea packetelor necesare

Distributivele moderne LaTeX vin cu un gama largă de pachete preinstalate. Pentru generarea evolventei eu mă voi folosi de packetele `pgfplots` și `amsmath`.

```latex
\usepackage{pgfplots,amsmath}
\pgfplotsset{compat=newest}
```

Pachetul `pgfplots` este un instrument puternic, bazat pe TikZ, dedicat construirii graficelor științifice. Acest pachet reprezintă un instrument de vizualizare pentru a simplifica includerea graficelor în documentele dvs. Ideea de bază este că furnizați datele/formula și pgfplots face restul [^pgfplots-overleaf].

Configurarea `\pgfplotsset{compat=newest}` ne permite să utilizăm cele mai recente caracteristici ale pachetului `pgfplots`.

Pachetul `amsmath` îl voi folosi pentru alinierea formulelor matematice, însă funcționalul acestui pachet nu se limitează doar la alinierea formulelor. Cu acest pachet puteți construi matrice, fracții continue (fracții incluse în fracții), formule în chenar și [multe altele](http://ctan.mirror.ftn.uns.ac.rs/macros/latex/required/amsmath/amsldoc.pdf).

## Definirea variabilelor necesare {#colors}

```latex
\pgfmathsetmacro\radius{2}

% Colors
\definecolor{tangentLineColor}{HTML}{BC5090}
\definecolor{remainingArcColor}{HTML}{003F5C}
\definecolor{involuteSplineColor}{HTML}{58508D}
\definecolor{accentColor}{HTML}{FF6361}
\colorlet{dashedLineColor}{black}

% Styles
\tikzstyle{information text}=[fill=red!10,inner sep=1ex]
\pgfplotsset{
 /pgf/number format/textnumber/.style={
  fixed,
  fixed zerofill,
  precision=2,
  1000 sep={,},
 },
}
```

În secțiunea dată setăm raza cercului. Toate calculele ulterioare for fi în bază de valoarea setată la variabila `radius`.

Ulterior, setăm culorile necesare pentru fiecare strat desenat pe graficul nostru. Aici se folosește pachetul `xcolor`. Dar de ce nu l-am importat în preambulă? Acest pachet nu trebuie în cazul nostru de importat din motiv ca `tikz` deja îl utilizează. Profit 🙂!

În ultimele comenzi din această secțiune se setează un stil cu denumirea `information text` ce va avea 10% intensitate din culoarea roșie și mai setează precizia părții fracționale a calculelor de 2 cifre.

## Construirea graficelor evolventei

Ca să construim animația evolventei unui cerc, vom proceda astfel. Prin comanda `\foreach` vom desena cadru după cadru câte un grafic unde ca valoare de iterație va fi unghiul de depanare a evolventei. Cu alte cuvinte, în fișierul de ieșire `pdf` vom avea in fiecare foaie a câte un grafic.

```latex
\foreach \rollAngle in {0.05,0.1,...,3.25}
{
  ...
}
```

Eu personal prefer să lucrez cu radiani în loc de grade și de aceea în ciclul `foreach` vedem că unghiul de depanare începe de la $ \psi_a = 0.05 rad $ și se termină cu $ \psi_b = 3.25 rad $. Pasul de la iterație la iterație este de $ \psi_i = 0.05 rad $. Putem cu aceste date prealabil să calculăm numărul de cadre.

$$
\frac{\psi_b - \psi_a}{\psi_i} = \frac{3.25 - 0.05}{0.05} = 64
$$

### Setări generale ale mediului tikzpicture fiecărui cadru {#tikzpicture}

Comenzile de desen `tikz` (inclusiv și `pgfplots`) trebuie să fie închise într-un mediu `tikzpicture`.

```latex
\begin{tikzpicture}
    [
    declare function = {
        involutex(\radius,\psi) = \radius * (cos(\psi) + \psi * sin(\psi));
        involutey(\radius,\psi) = \radius * (sin(\psi) - \psi * cos(\psi));
        arcx(\radius,\a,\psi) = \a + \radius * cos(\psi);
        arcy(\radius,\b,\psi) = \b + \radius * sin(\psi);
        },
    ]
    ...
\end{tikzpicture}
```

Ca opțiune a mediului `tikzpicture` noi vom declara funcțiile necesare pentru construirea graficelor. În cod vedem 4 funcții însă în realitate merge vorba de doar două, deoarece în perechi acestea alcătuiesc reprezentări parametrice.

> În matematică, o ecuație parametrică definește un grup de cantități ca funcții ale uneia sau mai multor variabile independente numite parametri. Ecuațiile parametrice sunt utilizate în mod obișnuit pentru a exprima coordonatele punctelor care alcătuiesc un obiect geometric, cum ar fi o curbă sau o suprafață, caz în care ecuațiile sunt numite colectiv reprezentare parametrică sau parametrizare (ortografiată alternativ ca parametrisare) a obiectului [^parametric-equation-wiki].

Ecuațiile parametrice pentru reprezentarea grafică a evolventei sunt indicate mai jos, unde $r$ este raza cercului și $\psi$ -- unghiul de "depanare a aței de pe mosor" 😄.

$$
x = r(\cos\psi + \psi\sin\psi)
$$

$$
y = r(\sin\psi - \psi\cos\psi)
$$

Celelalte două ecuații parametrice le vom folosi pentru a desena arcuri de cerc pe grafic, unde $r$ iarăși este raza cercului, $\psi$ -- unghiul arcului de cerc, iar $x_{\tiny 0}$ și $y_{\tiny 0}$ sunt coordinatele centrului cercului, în cazul în care acesta nu se află în origine.

$$
x = x_{\tiny 0} + r \cos\psi
$$

$$
y = y_{\tiny 0} + r \sin\psi
$$

### Adaugarea variabilelor suplimentare

La fiecare iterație vor fi efectuate careva calcule și rezultatele acestora vor fi stocate în variabile. Aceste variabile for fi de folos în continuare pentru afișarea textuală a rezultatelor calculelor.

```latex
\pgfmathsetmacro\rollAngleDeg{deg(\rollAngle)}
\pgfmathsetmacro\arcLength{0.5 * \rollAngle * \radius^2}
\pgfmathsetmacro\curvature{1 / (\radius * \rollAngle)}
```

Prima variabilă `rollAngleDeg` va stoca valoarea unghiului de depanare exprimată în grade.

Ulterior vom stoca lungimea arcului evolventei în variabila `arcLength`. Aceasta are următoarea formulă:

$$
L = \frac{1}{2} \psi r^2
$$

În final, vom calcula curbarea și o vom stoca în variabila `curvature`. Formula pentru calcularea acesteia este următoarea:

$$
\kappa = \frac{1}{\psi r}
$$

### Setări generale ale axelor graficului fiecărui cadru

Declarația de mediu `\begin {axis}` și `\end {axis}` va seta scalarea corectă a graficului. Noi vom folosi scalare simplă liniară, însă acest pachet are și [alte tipuri](https://www.overleaf.com/learn/latex/pgfplots_package#Reference_guide) de scalări care le puteți folosi pentru alte grafice.

```latex
\begin{axis}[
    name=plotAxis,
    trig format=rad,
    axis equal,
    axis lines=center,
    grid=both,
    xlabel=$x$,
    ylabel=$y$,
    xmin=-5,xmax=5,
    ymin=-3,ymax=7,
    xticklabels=\empty,
    yticklabels=\empty,
]
    ...
\end{axis}
```

După cum observăm axele au un șir de opțiuni atribuite. Pe scurt vom desfășura semnificația și utilitatea fiecăruia.

{{< image src="/images/2020/05/latex-involute-of-a-circle/involute-demo1.png" alt="Grafic cu axe localizate în centru, scalare liniară." caption="Grafic cu axe localizate în centru, scalare liniară.">}}

#### Opțiunea *name*

Opțiunea `name` setează numele graficului. Această opțiune ne va permite să afișăm în dreapta acestuia o casetă informativă cu toate calculele evolventei la fiecare pas.

#### Opțiunea *trig format=rad*

Pachetul `pgfplots` implicit lucrează cu `grade` în cazul când avem calcule ce conțin funcții trigonometrice. Eu am preferat lucrul cu `radiani`. Opțiunea `trig format` permite reconfigurarea formatului de intrare pentru funcții trigonometrice precum sinus, cosinus, tangentă și prietenii lor [^pgfplots-ctan-56].

#### Opțiunea *axis equal*

Cu ajutorul opțiunii `axis equal`, fiecare vector de unitate este setat la aceeași lungime, în timp ce dimensiunile axei rămân constante. După aceea, raporturile de mărime pentru fiecare unitate în `x` și `y` vor fi aceleași. Limitele axei vor fi extinse pentru a compensa efectul de scalare [^pgfplots-ctan-298].

#### Opțiunea *axis lines=center*

În mod implicit, liniile de axe sunt desenate ca o casetă, dar este posibil de modificat aspectul liniilor axelor `x` și `y`. Atribuirea unei din posibile valori ale acestei opțiuni, permite alegerea locației liniilor axelor graficului [^pgfplots-ctan-270-271].

Noi vom seta valoarea `center`, ceea ce va însemna că axele se vor insersecta în coordinata `0`.

#### Opțiunea *grid=both*

Această opțiune permite desenarea liniilor de grilă pe grafic.

#### Opțiunile *xlabel* și *ylabel*

Aceste opțiuni setează etichetele axei cu orice text de tip $ \TeX $.

#### Opțiunile *xmin*, *xmax*, *ymin* și *ymax*

Aceste opțiuni permit definirea limitelor axei, adică colțul din stânga jos și cel din dreapta sus. Tot ce se va afla în afara acestor limite va fi tăiat [^pgfplots-ctan-327].

#### Opțiunile *xticklabels* și *yticklabels*

Aceste opțiuni permit atribuirea etichetelor pentru fiecare pas a axei (segmente ale axelor). În cazul nostru, nu avem nevoie de etichetele cu numerotarea fiecărui segment al axelor. Pentru aceasta noi vom seta la aceste opțiuni valoarea `\empty` (gol).

### Adăugarea coordinatelor necesare pe grafic {#coordinates}

Ulterior, vom adăuga 3 coordinate pe grafic, și anume $O$, $L_{\tiny 1}$ și $L_{\tiny 2}$. Aceste coordinate ne vor permite să trasăm segmente.

Sintaxa de adăugare a coordinatei pe grafic este următoarea:

`\coordinate[<options>] (<name>) at (<coordinate>);`

Deci, coordinatele $O$, $L_{\tiny 1}$ și $L_{\tiny 2}$ vor fi adaugate astfel:

```latex
\coordinate (O) at (0,0);
\coordinate (L1) at ({arcx(\radius,0,\rollAngle)},{arcy(\radius,0,\rollAngle)});
\coordinate (L2) at ({involutex(\radius,\rollAngle)},{involutey(\radius,\rollAngle)});
```

Segmentul $OL_{\tiny 1}$ va reprezenta raza cercului, iar unghiul dintre acest segment și segmentul $[0,r]$ va fi însăși unghiul de depanare.

Segmentul $L_{\tiny 1}L_{\tiny 2}$ va reprezenta tangenta cercului, pornind de la perpendiculară spre punctul maxim al evolventei (calculând valoarile ecuațiilor parametrice în punctul `\rollAngle` de la fiecare iterație).

{{< image src="/images/2020/05/latex-involute-of-a-circle/involute-demo-coords.png" alt="Coordinatele O, L1 și L2 pe grafic." caption="Coordinatele $O$, $L_1$ și $L_2$ pe grafic.">}}

### Proiectarea arcului de cerc rămas după depanare

Fiindcă am spus că evolventa o putem reprezenta ca depanarea aței de pe mosor, atunci la fiecare iterație, noi vom elimina o parte din cerc care corespunde cu unghiul `\rollAngle`.

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo2.mp4" type="video/mp4">
    </video>
    <figcaption>Arcul de cerc rămas după depanare.</figcaption>
</figure>

Comanda `\addplot` este principala comandă de construire a graficelor, disponibilă în fiecare mediu de axe. Aceasta poate fi folosită de una sau mai multe ori în cadrul unei axe pentru a adăuga mai multe grafice [^pgfplots-ctan-43].

Sintaxa de adaugare a graficului pe axe este următoarea:

```latex
\addplot[<options>] <input data> <trailing path commands>;
```

Deci, pentru a constui graficul cu arcul de cerc rămas după depanare vom scrie următoarea comandă:

```latex
\addplot [domain=2*pi:\rollAngle,samples=200,remainingArcColor,thick,line cap=round]({arcx(\radius,0,x)},{arcy(\radius,0,x)});
```

Opțiunile setate la construirea graficului le vom desfășura în continuare, excepție fiind `remainingArcColor`. Această opțiune preia culoarea setată [în una din secțiunile anteriore](#colors).

#### Opțiunea *domain* {#domain-option}

Această opțiune ne permite de a seta domeniul de definiție al funcției. Expresiile graficelor bidimensionale sunt definite ca funcții $f: [x_{\tiny 1},x_{\tiny 2}] \to \mathbb{R}$ și $\langle x_{\tiny 1} \rangle$ și $\langle x_{\tiny 2} \rangle$ sunt setate cu opțiunea `domain` [^pgfplots-ctan-55].

În cazul nostru, domeniul de definiție este $f: [2\pi:\psi] \to \mathbb{R}$, unde $\psi$ este unghiul curent de depanare, egal cu valoarea variabilei `\rollAngle`.

Cu alte cuvinte, de la iterație la iterație cercul va pierde un arc, unghiul căruia va corespunde valorii variabilei `\rollAngle`.

#### Opțiunea *samples*

Această opțiune setează numărul de puncte de prelevare (sample points) [^pgfplots-ctan-56]. Este de menționat că aceste prelevări se vor conține în domeniul de definiție setat anterior.

#### Stilul TikZ *thick*

Această stil permite setarea lățimii liniei graficului. Stilul `thick`, pe care l-am selectat, corespunde cu lățimea de linie `0.8pt` [^tikz-wikibooks-line-width].

TikZ oferă lățimi de linie predefinite, după cum urmează [^pgfplots-ctan-190]:

* thin
* ultra thin
* very thin
* semithick
* thick
* very thick
* ultra thick

#### Opțiunea *line cap*

Această opțiune specifică modul în care liniile "se termină". Tipurile permise sunt `round`, `rect` și `butt`. Acestea au următoarele efecte [^tikz-ctan-175]:

{{< image src="/images/2020/05/latex-involute-of-a-circle/tikz-line-cap.png" alt="Tipurile de terminații ale liniilor." caption="Tipurile de terminații ale liniilor. Credits:  [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/base/doc/pgfmanual.pdf)">}}

Pentru reprezentarea grafică a tuturor ecuațiilor parametrice, vom folosi terminații de linii rotungite, adică vom folosi opțiunea `line cap=round`.

Deoarece am desfășurat fiecare opțiune, vom adăuga și celelalte grafice.

### Proiectarea arcului de cerc depanat

Prin comanda de mai jos, vom contstrui la fiecare iterație un arc de cerc punctat (opțiunea `dashedLineColor`), care va reprezenta unghiul de depanare al evoventei pe cerc.

Acest arc de cerc va avea domeniul de definiție exact invers cu cel [anterior](#domain-option), adică $f: [0:\psi] \to \mathbb{R}$.

```latex
\addplot [domain=0:\rollAngle,samples=200,dashedLineColor,dashed,line cap=round]({arcx(\radius,0,x)},{arcy(\radius,0,x)});
```

Ca rezultat, vizual vom avea un singur cerc, doar că odată cu mărirea unghiului de depanare se va puncta arcul de cerc.

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo3.mp4" type="video/mp4">
    </video>
    <figcaption>Proiectarea arcului de cerc depanat.</figcaption>
</figure>

### Proiectarea evolventei

Iată am ajuns și la cel mai important punct. Aici vom construi evolventa propriu-zisă. La construirea acesteia vom folosi ecuațiile parametrice discutate anterior [anterior](#tikzpicture).

```latex
\addplot [domain=0.01:\rollAngle,samples=200,involuteSplineColor,thick,line cap=round]({involutex(\radius,x)},{involutey(\radius,x)});
```

Unicul moment de menționat este că domeniul de definiție începe de la 0.01, deoarece întimpinam erori dacă începeam cu 0. Voi fi recunoscător pentru idei de ce se întimplă acest lucru 😏.

Ca rezultat, obținem profilul evolventei:

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo4.mp4" type="video/mp4">
    </video>
    <figcaption>Profilul evolventei pe grafic.</figcaption>
</figure>

### Proiectarea liniei ce unește tangenta cu capătul evolventei

Următorul pas va fi trasarea liniei care unește tangenta cu capătul evolventei.

Acest lucru îl vom realiza cu ajutorul comenzii `\draw`. Această linie va avea culoarea atribuită în variabila `tangentLineColor`, lățimea liniei va fi de tip `thick` și va avea coordinatele `L1` și `L2` care le-am declarat și inițializat în [una din secțiunile precedente](#coordinates).

```latex
\draw[tangentLineColor,thick] (L1) -- (L2);
```

Linia aceasta va reprezenta acea "ață" care o depanăm de pe mosor 🧵. Rezultatul arată astfel:

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo5.mp4" type="video/mp4">
    </video>
    <figcaption>Linia ce unește tangenta cu capătul evolventei.</figcaption>
</figure>

```latex
\draw[dashedLineColor,dashed] (O) -- (L1) node [accentColor,pos=0.5,sloped,above] {$r$};
```

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo6.mp4" type="video/mp4">
    </video>
    <figcaption>Proiectarea razei cercului.</figcaption>
</figure>

```latex
\addplot [domain=0:\rollAngle,samples=200,accentColor,line cap=round]({arcx(.4,0,x)},{arcy(.4,0,x)}) node[] at (.5, -.3) {$\psi$};
```

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo7.mp4" type="video/mp4">
    </video>
    <figcaption>Proiectarea unghiului depanării evolventei.</figcaption>
</figure>

```latex
\node [xshift=.5cm,below right,align=center,text width=6cm,style=information text] at (plotAxis.north east)
{
    This is a demonstration how the {\color{accentColor} involute of a circle} works.
    So, {\color{accentColor} $r$} is radius of the circle, {\color{accentColor} $\psi$} --- roll angle,
    {\color{accentColor} $L$} --- arc length and {\color{accentColor} $k$} -- curvature of
    the involute.
    \begin{align*}
        {\color{accentColor} r}      & = const                                                  &   & = \radius                                                    \\
        {\color{accentColor} \psi}   & = \pgfmathprintnumber[textnumber]{\rollAngle}\text{ rad} &   & \approx \pgfmathprintnumber[textnumber]{\rollAngleDeg}^\circ \\
        {\color{accentColor} L}      & = \frac{1}{2} \psi r^2                                   &   & = \pgfmathprintnumber[textnumber]{\arcLength}                \\
        {\color{accentColor} \kappa} & = \frac{1}{\psi r}                                       &   & \approx \pgfmathprintnumber[textnumber]{\curvature}
    \end{align*}
};
```

<figure>
    <video controls autoplay style="width: 100%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo8.mp4" type="video/mp4">
    </video>
    <figcaption>Afișarea parametrilor evolventei la fiecare iterație.</figcaption>
</figure>

[^standalone]: [Standalone: class vs package. StackOverflow](https://tex.stackexchange.com/a/287559)
[^standalone-package-1]: [Martin Scharrer. The standalone Package](http://mirrors.ibiblio.org/CTAN/macros/latex/contrib/standalone/standalone.pdf), v1.3a din 26.03.2018, p.1
[^standalone-package-8]: [Martin Scharrer. The standalone Package](http://mirrors.ibiblio.org/CTAN/macros/latex/contrib/standalone/standalone.pdf), v1.3a din 26.03.2018, p.8
[^tikz-ctan-175]: [Till Tantau și alti autori. TikZ & PGF. Manual for Version 3.1.5b](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/base/doc/pgfmanual.pdf), v3.1.5b din 08.01.2020, p.175
[^pgfplots-overleaf]: Pgfplots package. [Overleaf](https://www.overleaf.com/learn/latex/pgfplots_package)
[^pgfplots-ctan-43]: [Dr. Christian Feuersänger. Manual for Package pgfplots.](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf), v1.17 din 29.02.2020, p.43
[^pgfplots-ctan-56]: [Dr. Christian Feuersänger. Manual for Package pgfplots.](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf), v1.17 din 29.02.2020, p.56
[^pgfplots-ctan-270-271]: [Dr. Christian Feuersänger. Manual for Package pgfplots.](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf), v1.17 din 29.02.2020, p.270-271
[^pgfplots-ctan-298]: [Dr. Christian Feuersänger. Manual for Package pgfplots.](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf), v1.17 din 29.02.2020, p.298
[^pgfplots-ctan-327]: [Dr. Christian Feuersänger. Manual for Package pgfplots.](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf), v1.17 din 29.02.2020, p.327
[^pgfplots-ctan-55]: [Dr. Christian Feuersänger. Manual for Package pgfplots.](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf), v1.17 din 29.02.2020, p.55
[^pgfplots-ctan-190]: [Dr. Christian Feuersänger. Manual for Package pgfplots.](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf), v1.17 din 29.02.2020, p.190
[^parametric-equation-wiki]: Parametric equation. [Wikipedia](https://en.wikipedia.org/wiki/Parametric_equation)
[^tikz-wikibooks-line-width]: LaTeX/PGF/TikZ. Line width. [Wikibooks](https://en.wikibooks.org/wiki/LaTeX/PGF/TikZ#Line_width)
