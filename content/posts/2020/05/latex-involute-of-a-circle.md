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

## Definirea variabilelor necesare

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

### Setări generale ale graficului fiecărui cadru

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

<figure>
    <video style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo2.mp4" type="video/mp4">
    </video>
    <figcaption>test</figcaption>
</figure>

<figure>
    <video style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo3.mp4" type="video/mp4">
    </video>
    <figcaption>test</figcaption>
</figure>

<figure>
    <video style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo4.mp4" type="video/mp4">
    </video>
    <figcaption>test</figcaption>
</figure>

<figure>
    <video style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo5.mp4" type="video/mp4">
    </video>
    <figcaption>test</figcaption>
</figure>

<figure>
    <video style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo6.mp4" type="video/mp4">
    </video>
    <figcaption>test</figcaption>
</figure>

<figure>
    <video style="width: 70%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo7.mp4" type="video/mp4">
    </video>
    <figcaption>test</figcaption>
</figure>

<figure>
    <video controls autoplay style="width: 100%;max-height: 100%;">
        <source src="/images/2020/05/latex-involute-of-a-circle/involute-demo8.mp4" type="video/mp4">
    </video>
    <figcaption>test</figcaption>
</figure>

[^standalone]: [Standalone: class vs package. StackOverflow](https://tex.stackexchange.com/a/287559)
[^standalone-package-1]: [Martin Scharrer. The standalone Package](http://mirrors.ibiblio.org/CTAN/macros/latex/contrib/standalone/standalone.pdf), v1.3a din 26.03.2018, p.1
[^standalone-package-8]: [Martin Scharrer. The standalone Package](http://mirrors.ibiblio.org/CTAN/macros/latex/contrib/standalone/standalone.pdf), v1.3a din 26.03.2018, p.8
[^pgfplots-overleaf]: [Pgfplots package. Overleaf](https://www.overleaf.com/learn/latex/pgfplots_package)
