---
title: "Latex Involute of a Circle"
date: 2020-05-24T10:16:08+03:00
lastmod: 2020-05-24T10:16:08+03:00
draft: true

categories: ["Proiecte"]
tags: ["latex", "geometry", "mathematics", "trigonometry", "involute"]
---

## 1. Introducere

În acest articol, vom construi și anima evolventa unui cerc. Evolventa, vorbind cu un limbaj simplu, este o curbă care se obține prin desfășurarea imaginară de pe mosor a unei ațe permanent ținută întinsă sau procesul invers, înfășurarea acesteia pe mosor cu condiția ținerii în permanentă întindere a acesteia.

<figure>
    <video controls autoplay style="width: 100%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo8.mp4" type="video/mp4">
    </video>
    <figcaption>Demonstrare grafică cum evolventa funcționează.</figcaption>
</figure>

Evolventa este parte a profilul dintelui unei roți dințate folosite la transmisii prin angrenaje. Profilul evolventic asigură un raport de transmitere constant între roțile dințate, randament ridicat și altele.

{{< image src="https://upload.wikimedia.org/wikipedia/commons/c/c2/Involute_wheel.gif" alt="Raportul de transmitere constat între 2 roți dințate cu profil evolventic." caption="Raportul de transmitere constat între 2 roți dințate cu profil evolventic. Credits: [Wikipedia](https://en.wikipedia.org/wiki/Involute_gear#/media/File:Involute_wheel.gif)">}}

Codul complet al evolventei unui cerc îl găsiți mai jos sau pe [repository-ul Github](https://github.com/sunt-programator/latex-workpapers/blob/master/involute-of-circle/involute-demo.tex). În continuare vom explica mai detailat utilitatea fiecărei secțiuni de cod.

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

## 2. Setarea mediului de dezvoltare

Pentru development, vom folosi aplicația [Visual Studio Code](https://code.visualstudio.com/) în calitate de editor text.

Ulterior, vom folosi și seta [container Docker](https://www.docker.com/resources/what-container), în interiorul căruia vom instala și configura toate pachetele necesare pentru lucru.

Cu ajutorul editorului Visual Studio Code putem să facem development chiar în interiorul containerului 💡. Cum se configureaza `devcontainers` puteți citi în acest [articol](https://code.visualstudio.com/docs/remote/containers).

### 2.1. Configurarea *Dockerfile*

Docker poate construi în mod automat imagini citind instrucțiunile dintr-un fișier `Dockerfile`. Un fișier `Dockerfile` este un document text care conține toate comenzile pe care un utilizator le-ar putea apela din linia de comandă pentru a asambla o imagine [^dockerfile-reference].

Pentru development, vom folosi imaginea de bază [blang/latex](https://hub.docker.com/r/blang/latex/). Acesta va instala, compila și configura LaTeX și, astfel, vom avea toate pachetele LaTeX instalate în `devcontainer`-ul nostru.

Acest fișier îl vom plasa în mapa `.devcontainer` din proiectul nostru.

```dockerfile
FROM blang/latex:ubuntu

RUN apt update && apt install -y graphicsmagick ffmpeg
```

Pe lângă LaTeX, vom mai instala două pachete adiționare care se numesc [GraphicsMagick](http://www.graphicsmagick.org/) și [FFmpeg](https://ffmpeg.org/). Acestea vor servi la convertarea fișierului de ieșire `pdf`, generat de LaTex, în fișier `mp4`.

### 2.2. Configurarea *devcontainer.json*

La această etapă, vom crea fișierul `devcontainer.json` și, de asemenea, îl vom plasa în mapa `.devcontainer` din proiectul nostru. Acest fișier este utilizat pentru pentru lansarea (sau atașarea) containerului de dezvoltare (devcontainer). Aceast fișier va conține și comanda pentru instalarea in VS Code a extensiei [LaTeX Workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop), care are ca funcționalitate completarea automată a codului, syntax highlinting, compilarea documentului și multe altele.

```json
{
 "name": "LaTeX",
 "dockerFile": "Dockerfile",
 "settings": {
  "terminal.integrated.shell.linux": "/bin/bash",
  "latex-workshop.latex.watch.usePolling": true,
  "latex-workshop.latex.autoBuild.run": "onFileChange",
  "latex-workshop.latex.autoBuild.interval": 1000,
  "latex-workshop.docker.enabled": false,
 },
 "extensions": [
  "james-yu.latex-workshop",
  "adam-bender.commit-message-editor"
 ],
 "mounts": [
 ],
 "remoteEnv": {
 }
}
```

Dacă au fost efectuate configurările corecte, atunci la pornirea aplicației VS Code și la deschiderea mapei cu proiectul nostru, editorul ne va propune să trecem pe `devcontainer`.

{{< image src="/images/2020/05/latex-involute-of-a-circle/vscode-reopen-in-devcontainer.png" alt="Visual Studio Code propune de a deschide mapa în container." caption="Visual Studio Code propune de a deschide mapa în container.">}}

## 3. Structura de bază a documentului LaTeX și preambula acestuia

Pentru început este necesar de a crea un fișier cu extensia `.tex`. Toate comenzile ulterior le vom scrie în acesta.

Când $ \LaTeX $ procesează un document, el se așteaptă ca documentul să conțină o anumită structură. Astfel, fiecare document trebuie să conțină comenzile:

```latex
\documentclass{...}
\begin{document}
  ...
\end{document}
```

Între comenzile `\documentclass` și `\begin{document}` se afla așa numita preambulă. În secțiunea dată se conțin comenzile care vor afecta întregul document LaTeX. Tot aici se importează pachetele necesare și se fac careva setări asupra lor.

În cazul nostru, comanda `\documentclass` mai conține careva opțiuni, izolate între paranteze patrate și specifică ce tip de clasă se va folosi pentru document, aceasta fiind izolată între acolade.

```latex
\documentclass[tikz,border=10pt]{standalone}
```

### 3.1. Clasa și pachetul Standalone

Clasa `standalone` este proiectată pentru a crea fragmente individuale de conținut. Această clasă este utilă la generarea imaginilor care vor fi incluse în alte documente [^standalone].

Pachetul `standalone` permite utilizatorilor să plaseze cu ușurință imagini sau alt material în fișierele proprii și să le compileze de sine-stătător sau ca parte a unui document principal [^standalone-package-1].

### 3.2. Opțiunea și pachetul TikZ

Pachetul TikZ este probabil cel mai complex și puternic instrument de a crea elemente grafice în LaTeX. Cu acest pachet putem crea elemente grafice complexe folosind așa elemente simple ca linii, puncte, curbe, cercuri, dreptunghiuri, etc.

Pentru imaginile desenate cu TikZ este oferită o opțiune dedicată `tikz` care încarcă acest pachet și configurează mediul tikzpicture pentru a crea o singură pagină decupată [^standalone-package-8].

### 3.3. Opțiunea border

Opțiunea `border=10pt` specifică că documentul va avea un chenar de 10pt sau, altfel spus, va avea o margine din toate părțile de 10pt.

### 3.4. Importarea packetelor necesare

Distributivele moderne LaTeX vin cu un gama largă de pachete preinstalate. Pentru generarea evolventei ne vom folosi de packetele `pgfplots` și `amsmath`.

```latex
\usepackage{pgfplots,amsmath}
\pgfplotsset{compat=newest}
```

Pachetul `pgfplots` este un instrument puternic, bazat pe TikZ, dedicat construirii graficelor științifice. Acest pachet reprezintă un instrument de vizualizare pentru a simplifica includerea graficelor în documentele dvs. Ideea de bază este că furnizați datele/formula și pgfplots face restul [^pgfplots-overleaf].

Configurarea `\pgfplotsset{compat=newest}` ne permite să utilizăm cele mai recente caracteristici ale pachetului `pgfplots`.

Pachetul `amsmath` îl voi folosi pentru alinierea formulelor matematice, însă funcționalul acestui pachet nu se limitează doar la alinierea formulelor. Cu acest pachet puteți construi matrice, fracții continue (fracții incluse în fracții), formule în chenar și [multe altele](http://ctan.mirror.ftn.uns.ac.rs/macros/latex/required/amsmath/amsldoc.pdf).

## 4. Definirea variabilelor necesare {#colors}

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

## 5. Construirea graficelor evolventei

Ca să construim animația evolventei unui cerc, vom proceda astfel. Prin comanda `\foreach` vom desena cadru după cadru câte un grafic unde ca valoare de iterație va fi unghiul de depanare a evolventei. Cu alte cuvinte, în fișierul de ieșire `pdf` vom avea in fiecare foaie a câte un grafic.

```latex
\foreach \rollAngle in {0.05,0.1,...,3.25}
{
  ...
}
```

Pentru construirea evolventei vom folosi radiani în loc de grade și de aceea în ciclul `foreach` vedem că unghiul de depanare începe de la $ \psi_a = 0.05 rad $ și se termină cu $ \psi_b = 3.25 rad $. Pasul de la iterație la iterație este de $ \psi_i = 0.05 rad $. Putem cu aceste date prealabil să calculăm numărul de cadre.

$$
\frac{\psi_b - \psi_a}{\psi_i} = \frac{3.25 - 0.05}{0.05} = 64
$$

### 5.1. Setări generale ale mediului tikzpicture fiecărui cadru {#tikzpicture}

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

Celelalte două ecuații parametrice le vom folosi pentru a desena arcuri de cerc pe grafic, unde $r$ iarăși este raza cercului, $\psi$ -- unghiul arcului de cerc, iar $x_{\tiny 0}$ și $y_{\tiny 0}$ sunt coordonatele centrului cercului, în cazul în care acesta nu se află în origine.

$$
x = x_{\tiny 0} + r \cos\psi
$$

$$
y = y_{\tiny 0} + r \sin\psi
$$

### 5.2. Adaugarea variabilelor suplimentare

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

### 5.3. Setări generale ale axelor graficului fiecărui cadru

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

#### 5.3.1. Opțiunea *name*

Opțiunea `name` setează numele graficului. Această opțiune ne va permite să afișăm în dreapta acestuia o casetă informativă cu toate calculele evolventei la fiecare pas.

#### 5.3.2. Opțiunea *trig format=rad*

Pachetul `pgfplots` implicit lucrează cu `grade` în cazul când avem calcule ce conțin funcții trigonometrice. Noi, însă, vom folosi `radiani`. Opțiunea `trig format` permite reconfigurarea formatului de intrare pentru funcții trigonometrice precum sinus, cosinus, tangentă și prietenii lor [^pgfplots-ctan-56].

#### 5.3.3. Opțiunea *axis equal*

Cu ajutorul opțiunii `axis equal`, fiecare vector de unitate este setat la aceeași lungime, în timp ce dimensiunile axei rămân constante. După aceea, raporturile de mărime pentru fiecare unitate în `x` și `y` vor fi aceleași. Limitele axei vor fi extinse pentru a compensa efectul de scalare [^pgfplots-ctan-298].

#### 5.3.4. Opțiunea *axis lines=center*

În mod implicit, liniile de axe sunt desenate ca o casetă, dar este posibil de modificat aspectul liniilor axelor `x` și `y`. Atribuirea unei din posibile valori ale acestei opțiuni, permite alegerea locației liniilor axelor graficului [^pgfplots-ctan-270-271].

Noi vom seta valoarea `center`, ceea ce va însemna că axele se vor insersecta în coordonata `0`.

#### 5.3.5. Opțiunea *grid=both*

Această opțiune permite desenarea liniilor de grilă pe grafic.

#### 5.3.6. Opțiunile *xlabel* și *ylabel*

Aceste opțiuni setează etichetele axei cu orice text de tip $ \TeX $.

#### 5.3.7. Opțiunile *xmin*, *xmax*, *ymin* și *ymax*

Aceste opțiuni permit definirea limitelor axei, adică colțul din stânga jos și cel din dreapta sus. Tot ce se va afla în afara acestor limite va fi tăiat [^pgfplots-ctan-327].

#### 5.3.8. Opțiunile *xticklabels* și *yticklabels*

Aceste opțiuni permit atribuirea etichetelor pentru fiecare pas a axei (segmente ale axelor). În cazul nostru, nu avem nevoie de etichetele cu numerotarea fiecărui segment al axelor. Pentru aceasta noi vom seta la aceste opțiuni valoarea `\empty` (gol).

### 5.4. Adăugarea coordonatelor necesare pe grafic {#coordonates}

Ulterior, vom adăuga 3 coordonate pe grafic, și anume $O$, $L_{\tiny 1}$ și $L_{\tiny 2}$. Aceste coordonate ne vor permite să trasăm segmente.

Sintaxa de adăugare a coordonatei pe grafic este următoarea:

`\coordonate[<options>] (<name>) at (<coordonate>);`

Deci, coordonatele $O$, $L_{\tiny 1}$ și $L_{\tiny 2}$ vor fi adaugate astfel:

```latex
\coordonate (O) at (0,0);
\coordonate (L1) at ({arcx(\radius,0,\rollAngle)},{arcy(\radius,0,\rollAngle)});
\coordonate (L2) at ({involutex(\radius,\rollAngle)},{involutey(\radius,\rollAngle)});
```

Segmentul $OL_{\tiny 1}$ va reprezenta raza cercului, iar unghiul dintre acest segment și segmentul $[0,r]$ va fi însăși unghiul de depanare.

Segmentul $L_{\tiny 1}L_{\tiny 2}$ va reprezenta tangenta cercului, pornind de la perpendiculară spre punctul maxim al evolventei (calculând valoarile ecuațiilor parametrice în punctul `\rollAngle` de la fiecare iterație).

{{< image src="/images/2020/05/latex-involute-of-a-circle/involute-demo-coords.png" alt="coordonatele O, L1 și L2 pe grafic." caption="coordonatele $O$, $L_1$ și $L_2$ pe grafic.">}}

### 5.5. Proiectarea arcului de cerc rămas după depanare {#remaining-arc-circle-plot}

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

#### 5.5.1. Opțiunea *domain* {#domain-option}

Această opțiune ne permite de a seta domeniul de definiție al funcției. Expresiile graficelor bidimensionale sunt definite ca funcții $f: [x_{\tiny 1},x_{\tiny 2}] \to \mathbb{R}$ și $\langle x_{\tiny 1} \rangle$ și $\langle x_{\tiny 2} \rangle$ sunt setate cu opțiunea `domain` [^pgfplots-ctan-55].

În cazul nostru, domeniul de definiție este $f: [2\pi:\psi] \to \mathbb{R}$, unde $\psi$ este unghiul curent de depanare, egal cu valoarea variabilei `\rollAngle`.

Cu alte cuvinte, de la iterație la iterație cercul va pierde un arc, unghiul căruia va corespunde valorii variabilei `\rollAngle`.

#### 5.5.2. Opțiunea *samples*

Această opțiune setează numărul de puncte de prelevare (sample points) [^pgfplots-ctan-56]. Este de menționat că aceste prelevări se vor conține în domeniul de definiție setat anterior.

#### 5.5.3. Stilul TikZ *thick*

Această stil permite setarea lățimii liniei graficului. Stilul `thick`, pe care l-am selectat, corespunde cu lățimea de linie `0.8pt` [^tikz-wikibooks-line-width].

TikZ oferă lățimi de linie predefinite, după cum urmează [^pgfplots-ctan-190]:

* thin
* ultra thin
* very thin
* semithick
* thick
* very thick
* ultra thick

#### 5.5.4. Opțiunea *line cap*

Această opțiune specifică modul în care liniile "se termină". Tipurile permise sunt `round`, `rect` și `butt`. Acestea au următoarele efecte [^tikz-ctan-175]:

{{< image src="/images/2020/05/latex-involute-of-a-circle/tikz-line-cap.png" alt="Tipurile de terminații ale liniilor." caption="Tipurile de terminații ale liniilor. Credits:  [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/base/doc/pgfmanual.pdf)">}}

Pentru reprezentarea grafică a tuturor ecuațiilor parametrice, vom folosi terminații de linii rotungite, adică vom folosi opțiunea `line cap=round`.

Deoarece am desfășurat fiecare opțiune, vom adăuga și celelalte grafice.

### 5.6. Proiectarea arcului de cerc depanat

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

### 5.7. Proiectarea evolventei

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

### 5.8. Proiectarea liniei ce unește tangenta cu capătul evolventei

Următorul pas va fi trasarea liniei care unește tangenta cu capătul evolventei.

Acest lucru îl vom realiza cu ajutorul comenzii `\draw`. Această linie va avea culoarea atribuită în variabila `tangentLineColor`, lățimea liniei va fi de tip `thick` și va avea coordonatele `L1` și `L2` care le-am declarat și inițializat în [una din secțiunile precedente](#coordonates).

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

### 5.9. Proiectarea razei cercului

Tot cu aceeași sintaxă vom proiecta raza cercului care se va roti odată cu mărirea unghiului de depanare.

```latex
\draw[dashedLineColor,dashed] (O) -- (L1) node [accentColor,pos=0.5,sloped,above] {$r$};
```

Rezultatul îl putem vedea în animația de mai jos, însă opțiunile pe care le-am setat, le vom desfășura în secțiunile următoare.

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo6.mp4" type="video/mp4">
    </video>
    <figcaption>Proiectarea razei cercului.</figcaption>
</figure>

#### 5.9.1. Opțiunea */tikz/pos*

Opțiunea `/tikz/pos=<fraction>` ancorează nodul pe un anumit punct de pe linie de la coordonata anterioară la punctul actual. $\langle fraction \rangle$ dictează cât de "departe" trebuie să fie punctul pe linie. O $\langle fraction \rangle$ setată ca $0$ este coordonata anterioară, $1$ este cea curentă, iar toate celelalte sunt între ele. În special, $0.5$ este mijlocul [^tikz-ctan-246].

Noi vom seta valoarea $0.5$, ceea ce va însemna că nodul se afla la mijloc de linie. Același lucru îl putem face cu opțiunea `/tikz/midway`, care este echivalentul opțiunii `pos=0.5`.

#### 5.9.2. Opțiunea */tikz/sloped*

Opțiunea `/tikz/sloped` face ca nodul să fie rotit, astfel încât linia orizontală a acestuia să devină tangentă cu curba. Rotirea de obicei se face în așa mod, încât textul să nu fie niciodată "cu susul în jos". [^tikz-ctan-248].

{{< image src="/images/2020/05/latex-involute-of-a-circle/tikz-sloped.png" alt="Opțiunea slopped din pachetul TikZ." caption="Opțiunea `/tikz/sloped` din pachetul TikZ. Credits:  [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/base/doc/pgfmanual.pdf)">}}

În cazul nostru avem nu o curbă, ci o linie și textul trebuie să se rotească odată cu rotirea liniei. La momentul când unghiul de depanare va depăși $\frac{\pi}{2}$ radiani sau $90^{\circ}$, această opțiune nu va permite ca textul să fie inversat (cu susul în jos).

#### 5.9.3. Opțiunea */tikz/above*

Această opțiune este echivalentă cu opțiunea `/tikz/anchor=south` și permite poziționarea nodului deasupra liniei.

### 5.10. Proiectarea unghiului arculului de cerc depanat

La această etapă, vom proiecta unghiul arcului de cerc depanat. Pentru aceasta, vom utiliza comanda `\addplot`, sintaxa căreia am desfășurat-o în una din [secțiunile anterioare](#remaining-arc-circle-plot). Unica diferență este că aici adăugăm un nod fix poziționat în punctul $(0.5,-0.3)$ cu textul $\psi$.

```latex
\addplot [domain=0:\rollAngle,samples=200,accentColor,line cap=round]({arcx(.4,0,x)},{arcy(.4,0,x)}) node[] at (.5, -.3) {$\psi$};
```

Desigur că $\LaTeX$ dispune de o gamă largă de pachete pentru desenarea unghiurilor (cum ar fi pachetul [tkz-euclide](http://ctan.mirror.ftn.uns.ac.rs/macros/latex/contrib/tkz/tkz-euclide/doc/TKZdoc-euclide.pdf)), însă vom merge pe calea proiectării aceluiași arc de cerc, numai că cu o rază mai mică.

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo7.mp4" type="video/mp4">
    </video>
    <figcaption>Proiectarea unghiului depanării evolventei.</figcaption>
</figure>

### 5.11. Afișarea parametrilor evolventei la fiecare iterație

Parametrii evolventei la fiecare iterație vor fi poziționați într-o casetă, ultima fiind poziționată în dreapta graficului nostru.

<figure>
    <video controls autoplay style="width: 100%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo8.mp4" type="video/mp4">
    </video>
    <figcaption>Afișarea parametrilor evolventei la fiecare iterație.</figcaption>
</figure>

Codul casetei cu parametrii evolventei îl putem vedea mai jos:

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

Această porțiune de cod de la prima vedere pare a fi dificilă. În secțiunile ulterioare vom explica unele momente-cheie ce au loc în acest fragment de cod.

#### 5.11.1. Commanda *\node*

Nodurile sunt probabil cele mai universale elemente din `TikZ`. Un nod este de obicei un dreptunghi sau un cerc sau o altă formă simplă cu un text pe el. În cel mai simplu caz, un nod este doar un text care este plasat la o anumită coordonată.

```latex
\node[<options>](<name>) at (<coordinate>){<text>};
```

În cazul nostru, vom crea un nod cu coordonata localizată în colțul drept sus al graficului principal. Acest lucru se realizează prin referirea către numele axei graficului principal, cu indicarea ancorei (punctului de referință a nodului) în poziția nord-vest.

{{< image src="/images/2020/05/latex-involute-of-a-circle/tikz-anchor-point.png" alt="Ancore poziționate pe caseta de delimitare a axei din pachetul TikZ." caption="Ancore poziționate pe caseta de delimitare a axei din pachetul TikZ. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf)">}}

Opțiunea `xshift=.5cm` permite de a executa translația casetei pe axa $x$ cu `0.5cm`, `below right` -- poziționarea casetei în dreapta sub coordonata setată anterior și cu luarea în considerare a translației efectuate.

Opțiunea `/tikz/text width=6cm` va plasa textul nodului într-o casetă de `6cm` lățime. Dacă lățimea textului va depăși această limită, atunci se va întrerupe linia și se va trece conținutul rămas din rând nou.

În ceea ce privește opțiunea `/tikz/align=center`, aceasta este utilizată pentru a configura alinierea textului cu mai multe linii în interiorul unui nod. Dacă opțiunea `/tikz/text width` este setată la o anumită lățime (să numim această aliniere cu line breaking), opțiunea de aliniere va configura `\leftskip` și `\rightskip` în așa fel încât textul să fie întrerupt și aliniat în funcție de opțiunea de aliniere [^tikz-ctan-235].

Opțiunea `style=information text` permite de a seta stilul pe care l-am identifiat [în una din secțiunile anteriore](#colors). Această casetă cu parametrii evolventei la fiecare iterație va avea o culoare de fundal roșie cu intensitatea de 10% din culoarea de bază.

#### 5.11.2. Afișarea textului color

Pentru afișarea unui text color în nod, putem utiliza sintaxa de mai jos iar denumirile culorilor noi le-am identificat [în primele secțiuni](#colors).

```latex
{\color{accentColor} some text}
```

#### 5.11.3. Alinierea formulelor matematice din casetă

Formulele matematice nu vor fi aliniate într-o formă simplă (stânga, centru, dreapta), ci va avea o formă complexă. Alinierea se va face la simbolul `=`, cu alte cuvinte toate cele 4 formule se vor poziționa una sub alta cu alinierea strict la acest simbol.

{{< image src="/images/2020/05/latex-involute-of-a-circle/involute-demo-text-align.png" alt="Alinierea formulelor matematice după simbolul egal." caption="Alinierea formulelor matematice după simbolul egal.">}}

Acest lucru se face cu ajutorul pachetului `amsmath`, folosind contrucția `\begin{align*} ... \end{align*}` și determinând prin simbolul `&` locul unde avem nevoie să aliniem ecuația.

```latex
\begin{align*}
    {\color{accentColor} r} & = const & & = \radius \\
\end{align*}
```

Despre semnificația și utilitatea simbolului `&` în acest pachet puteți citi [aici](https://tex.stackexchange.com/a/159724).

## 6. Producerea fișierului de ieșire final cu animarea evolventei

Dat fiind faptului că lucrăm în `devContainer`, deja avem toate pachetele instalate pentru convertirea fișierului `pdf` în `mp4` (fișier video cu animarea evolventei). Era posibilă convertirea în fișier `gif` dar acest format este unul învechit și are [o serie de dezavantaje](https://connectusfund.org/6-advantages-and-disadvantages-of-animated-gifs).

Există o mulțime de formate mai performante, cum ar fi [webp](https://en.wikipedia.org/wiki/WebP), [apng](https://en.wikipedia.org/wiki/APNG) și altele. Nu vom folosi aceste formate, fiindcă problema constă în compatibilitate. Aceste formate nu sunt suportate pe deplin de toate browserele (exemplu pentru [apng](https://caniuse.com/#feat=apng), [webp](https://caniuse.com/#feat=webp)).

Cea mai optimă variantă este `mp4`. Aceast format și codecul `H.264` al acestuia este suportat practic de [toate browserele](https://caniuse.com/#feat=mpeg4). Putem seta și opțiunea [loop](https://www.geeksforgeeks.org/html-video-loop-attribute/) pentru repetarea ciclică a video-ului si, astfel, vom obține același efect ca și în cazul unui fișier de tip `gif`.

Prima etapă este convertirea fișierului `pdf` generat de LaTeX într-o secvență de imagini cu ajutorul pachetului [GraphicsMagick](http://www.graphicsmagick.org/). Cu alte cuvinte, fiecare foaie din fișier fa fi salvată în imagini distincte cu extensia `png`.

Pentru a realiza această convertire, ne vom folosi de comanda de mai jos, care va salva secvențe de imagini cu densitatea de `300 DPI` și fonul alb.

```bash
mkdir involute-of-circle/output/

gm convert -density 300 involute-of-circle/involute-demo.pdf -background white +adjoin involute-of-circle/output/image_%02d.png
```

Următorul pas este convertirea secvenței de imagini în fișier video de tip `mp4`. Pentru aceasta, ne vom folosi de pachetul preinstalat in container care se numește [FFmpeg](https://ffmpeg.org/).

```bash
ffmpeg -r 15 -i involute-of-circle/09/image_%02d.png -c:v libx264 -vf fps=60 -pix_fmt yuv420p -vf "pad=ceil(iw/2)*2:ceil(ih/2)*2" involute-of-circle/09/out.mp4
```

## 7. Concluzie

$\LaTeX$ este un sistem avansat de preparare a documentului. Acesta dispune de un număr larg de pachete care permit realizarea oricărui lucru.

În acest articol am folosit pachetul `PGFPlots` (care la rânul lui foloseste pachetul `TikZ`), pentru a desena evolventa unui cerc.

Animarea evolventei am realizat-o cu ajutorul ciclului `foreach`, unde la fiecare iterație am modificat unghiul de depanare. Ca rezultat am obținut un fișier `pdf` cu cadrele necesare pentru animare. Ulterior, acest fișier l-am convertat în fișier `mp4` cu ajutorul pachetului [GraphicsMagick](http://www.graphicsmagick.org/).

<figure>
    <video controls autoplay style="width: 100%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo8.mp4" type="video/mp4">
    </video>
    <figcaption>Rezultatul final.</figcaption>
</figure>

Codul deplin se află pe [repository Github](https://github.com/sunt-programator/latex-workpapers).

[^standalone]: [Standalone: class vs package. StackOverflow](https://tex.stackexchange.com/a/287559)
[^standalone-package-1]: Martin Scharrer. The standalone Package, v1.3a din 26.03.2018, p.1. Credits: [CTAN](http://mirrors.ibiblio.org/CTAN/macros/latex/contrib/standalone/standalone.pdf)
[^standalone-package-8]: Martin Scharrer. The standalone Package, v1.3a din 26.03.2018, p.8. Credits: [CTAN](http://mirrors.ibiblio.org/CTAN/macros/latex/contrib/standalone/standalone.pdf)
[^tikz-ctan-175]: Till Tantau și alti autori. TikZ & PGF. Manual for Version 3.1.5b, v3.1.5b din 08.01.2020, p.175. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/base/doc/pgfmanual.pdf)
[^tikz-ctan-235]: Till Tantau și alti autori. TikZ & PGF. Manual for Version 3.1.5b, v3.1.5b din 08.01.2020, p.235. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/base/doc/pgfmanual.pdf)
[^tikz-ctan-246]: Till Tantau și alti autori. TikZ & PGF. Manual for Version 3.1.5b, v3.1.5b din 08.01.2020, p.246. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/base/doc/pgfmanual.pdf)
[^tikz-ctan-248]: Till Tantau și alti autori. TikZ & PGF. Manual for Version 3.1.5b, v3.1.5b din 08.01.2020, p.248. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/base/doc/pgfmanual.pdf)
[^pgfplots-overleaf]: Pgfplots package. Credits: [Overleaf](https://www.overleaf.com/learn/latex/pgfplots_package)
[^pgfplots-ctan-43]: Dr. Christian Feuersänger. Manual for Package pgfplots, v1.17 din 29.02.2020, p.43. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf)
[^pgfplots-ctan-56]: Dr. Christian Feuersänger. Manual for Package pgfplots, v1.17 din 29.02.2020, p.56. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf)
[^pgfplots-ctan-270-271]: Dr. Christian Feuersänger. Manual for Package pgfplots, v1.17 din 29.02.2020, p.270-271. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf)
[^pgfplots-ctan-298]: Dr. Christian Feuersänger. Manual for Package pgfplots, v1.17 din 29.02.2020, p.298. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf)
[^pgfplots-ctan-327]: Dr. Christian Feuersänger. Manual for Package pgfplots, v1.17 din 29.02.2020, p.327. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf)
[^pgfplots-ctan-55]: Dr. Christian Feuersänger. Manual for Package pgfplots, v1.17 din 29.02.2020, p.55. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf)
[^pgfplots-ctan-190]: Dr. Christian Feuersänger. Manual for Package pgfplots, v1.17 din 29.02.2020, p.190. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf)
[^parametric-equation-wiki]: Parametric equation. Credtits: [Wikipedia](https://en.wikipedia.org/wiki/Parametric_equation)
[^tikz-wikibooks-line-width]: LaTeX/PGF/TikZ. Line width. Credtits: [Wikibooks](https://en.wikibooks.org/wiki/LaTeX/PGF/TikZ#Line_width)
[^dockerfile-reference]: Dockerfile reference. Credtits: [docs.docker.com](https://docs.docker.com/engine/reference/builder/)
