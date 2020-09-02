---
title: "LaTeX: Drawing and animating the involute of a circle"
date: 2020-08-09T21:21:28+03:00
draft: true
summary: In this article we'll discuss the construction and animation of the involute of a circle. The involute, speaking in simple language, can be imaginarily represented as winding thread around a circle.
featuredImage: "/images/2020/08/latex-involute-of-a-circle/latex-involute-of-a-circle.png"

categories: ["Proiecte"]
tags: ["latex", "geometry", "mathematics", "trigonometry", "involute", "graphicsmagick", "imagemagick"]
description: In this article we'll discuss the construction and animation of the involute of a circle. The involute, speaking in simple language, can be imaginarily represented as winding thread around a circle.

images: ["/images/2020/08/latex-involute-of-a-circle/latex-involute-of-a-circle.png"]
videos: ["/images/2020/08/latex-involute-of-a-circle/involute-demo8.mp4"]
---

## 1. Introduction {#introduction}

Hi all! 🙋‍♂️

In this article we'll discuss the construction and animation of the involute of a circle. The involute, speaking in simple language, can be imaginarily represented as winding thread around a circle.

{{< youtube b8XjwuqPkRk >}}

The involute is a part of the tooth profile of a gear used for gear transmissions. The involute profile ensures a constant gear ratio between gears, high efficiency, as well as other advantages.

{{< image src="/images/2020/08/latex-involute-of-a-circle/Involute_wheel.gif" alt="Constant transmission ratio between 2 gears with involute profile." caption="Constant transmission ratio between 2 gears with involute profile. Credits: [Wikipedia](https://en.wikipedia.org/wiki/Involute_gear#/media/File:Involute_wheel.gif)">}}

We'll design the involute of a circle using [LaTeX](https://en.wikipedia.org/wiki/LaTeX), the document preparation system that is widely used in academia.

<figure>
    <video controls autoplay style="width: 100%;max-height: 100%;">
        <source src="/images/2020/08/latex-involute-of-a-circle/involute-demo8.mp4" type="video/mp4">
    </video>
    <figcaption>Graphic demonstration of how the involute of a circle works.</figcaption>
</figure>

$\LaTeX$ is well known for its skill with mathematical and scientific text and other difficult typesetting jobs: long or complicated and multi-lingual documents. systems produce output --- on paper or on the computer screen --- of the highest typographic quality. This quality is crucial for complex texts, where the reader's ability to understand the material depends on the clarity with which it is presented [^tex-friends].

The complete code for designing the involute of a circle can be found below or on the [Github repository](https://github.com/sunt-programator/latex-workpapers/blob/master/involute-of-circle/involute-demo.tex). Below we'll explain in more detail the usefulness of each section of code.

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

## 2. Setting the development environment {#environment-settings}

For development purposes, we'll use the free editor [Visual Studio Code](https://code.visualstudio.com/) and we'll create a [Docker container](https://www.docker.com/resources/what-container), inside which we'll install and configure all the necessary packages for work.

With Visual Studio Code, we can do development right inside the container. You can read in this [article](https://code.visualstudio.com/docs/remote/containers) how to configure `devcontainers`.

### 2.1. *Dockerfile* configurations {#dockerfile-configuration}

Docker can build images automatically by reading the instructions from a `Dockerfile`. A `Dockerfile` is a text document that contains all the commands a user could call on the command line to assemble an image [^dockerfile-reference].

In order to carry out the development, we'll use the basic [blang/latex](https://hub.docker.com/r/blang/latex/) image. It will install, compile and configure `LaTeX`. Thus, we'll have all LaTeX packages installed in our `devcontainer`.

We'll place this file in the folder `.devcontainer` of our project.

```dockerfile
FROM blang/latex:ubuntu

RUN apt update && apt install -y graphicsmagick ffmpeg
```

In addition to LaTeX, we'll install two additional packages called [GraphicsMagick](http://www.graphicsmagick.org/) and [FFmpeg](https://ffmpeg.org/). They will be used to convert the output `pdf` file, generated by LaTeX, into a `mp4` file.

### 2.2. *devcontainer.json* configurations {#devcontainer-configuration}

At this stage, we'll create the file `devcontainer.json` that we'll also place in the `.devcontainer` project folder. This file is used to launch (or attach) the development container (devcontainer). This file will also contain the command to install the [LaTeX Workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop) extension in VS Code, which has the functionality of automatic code completion, highliting syntax, document compilation and many other functionalities.

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

If the correct configurations have been made, then when starting the VS Code application and opening the folder with the given project, the editor will suggest us to go on `devcontainer`.

{{< image src="/images/2020/08/latex-involute-of-a-circle/vscode-reopen-in-devcontainer.png" alt="Visual Studio Code proposes to open the folder in the container." caption="Visual Studio Code proposes to open the folder in the container.">}}

## 3. Basic structure and preamble of the LaTeX document {#basic-latex-settings}

To start, we need to create a file with the extension `.tex`. All the necessary instructions for building the involute will be written in it.

When $\LaTeX$ processes a document, it expects the document to contain a certain structure. Thus, each document must contain the commands:

```latex
\documentclass{...}
\begin{document}
  ...
\end{document}
```

Between the commands `\documentclass` and the `\begin{document}` is the so-called preamble. This section contains commands that will affect the entire LaTeX document. Also here the necessary packages are imported and some settings are made on them.

In our case, the command `\documentclass` also contains several options, isolated in square brackets and specific what type of document class will be used, this being isolated in brackets.

```latex
\documentclass[tikz,border=10pt]{standalone}
```

### 3.1. *standalone* class and package {#standalone-class}

The `standalone` class is designed to create individual snippets of content whose size adapts to the content itself. This class is useful especially if you are generating images for inclusion into other documents, especially because it can also do automatic conversion to other image formats. [^standalone].

The `standalone` bundle allows users to easily place picture environments or other material in own source files and compile these on their own or as part of a main document [^standalone-package-1].

### 3.2. *TikZ* option and package {#tikz-package}

The TikZ package is probably the most complex and powerful tool to create graphic elements in LaTeX. With this package we can create complex graphic elements using such simple elements as lines, points, curves, circles, rectangles, etc.

For pictures drawn with `TikZ` a dedicated tikz option is provided which loads the tikz package and also configures the `tikzpicture` environment to create a single cropped page [^standalone-package-8].

### 3.3. *border* option {#border-option}

The `border=10pt` option specifies that the document will have a 10pt border or, in other words, will have a 10pt margin on all sides.

### 3.4. Importing necessary packages {#packages-importing}

Modern LaTeX distributions come with a wide range of pre-installed packages. To generate the involute we'll use the `pgfplots` and `amsmath` packages.

```latex
\usepackage{pgfplots,amsmath}
\pgfplotsset{compat=newest}
```

The `pgfplots` package is a powerful tool, based on `TikZ`, dedicated to create scientific graphs. This package represents a visualization tool to make simpler the inclusion of plots in your documents. The basic idea is that we provide the input data/formula and `pgfplots does the rest [^pgfplots-overleaf].

The configuration `\pgfplotsset{compat=newest}` allows us to use the latest features of the `pgfplots` package.

The `amsmath` package we'll use to align the mathematical formulas, but the functionality of this package is not limited to the alignment of the formulas. With this package you can build matrices, continued fractions (fractions included in fractions), boxed formulas and [much more](http://ctan.mirror.ftn.uns.ac.rs/macros/latex/required/amsmath/amsldoc.pdf).

## 4. Defining the necessary variables {#colors}

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

In this section we set the radius of the circle. All subsequent calculations will be based on the value set to the `radius` variable.

Next, we set the required colors for each layer drawn on our plot. Here we'll use the `xcolor` package. But why didn't we import him in the preamble? This package does not need to be imported in our case because because `tikz` is already using it. Profit 🙂!

The last commands in this section set a style with the name `information text` that will have 10% intensity of the original red color and also set the precision of the fractional part.

## 5. Building involute plots {#involute-plotting}

To build the animation of the involute of a circle, we'll do so. Through the command `\foreach` we'll draw frame by frame a plot where the iteration value will be the roll angle of the involute. In other words, in the output file `pdf` we'll have a plot on each page.

```latex
\foreach \rollAngle in {0.05,0.1,...,3.25}
{
  ...
}
```

To build the involute we'll use `radians` instead of `degrees`. In the `foreach` loop we see that the roll angle starts at $ \psi_a = 0.05 rad $ and ends at $ \psi_b = 3.25 rad $. The step from iteration to iteration is $ \psi_i = 0.05 rad $. With this data we can calculate preliminary the number of frames that will be in the end.

$$
\frac{\psi_b - \psi_a}{\psi_i} = \frac{3.25 - 0.05}{0.05} = 64
$$

### 5.1. General *tikzpicture* environmental settings at each iteration {#tikzpicture}

The drawing commands `tikz` (including also `pgfplots`) must be included in a `tikzpicture` environment.

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

As a `tikzpicture` environment option we'll determine the functions needed to build the plots. In the code we see 4 functions but in reality there are only two, because in pairs they make up parametric equations.

> In mathematics, a parametric equation defines a group of quantities as functions of one or more independent variables called parameters. Parametric equations are commonly used to express the coordinates of the points that make up a geometric object such as a curve or surface, in which case the equations are collectively called a parametric representation or parameterization (alternatively spelled as parametrisation) of the object [^parametric-equation-wiki].

The parametric equations for the graphical representation of the involute are indicated below, where $r$ is the radius of the circle and $\psi$ -- the angle of "debugging the thread from the spool" 😄.

$$
x = r(\cos\psi + \psi\sin\psi)
$$

$$
y = r(\sin\psi - \psi\cos\psi)
$$

We'll use the other two parametric equations to draw arcs of a circle on the plot, where $r$ also is the radius of the circle, $\psi$ -- the angle of the arc of the circle, and $x_{\tiny 0}$ and $y_{\tiny 0}$ are the coordinates of the center of the arc, whether it's not placed at origin.

$$
x = x_{\tiny 0} + r \cos\psi
$$

$$
y = y_{\tiny 0} + r \sin\psi
$$

### 5.2. Adding additional variables {#additional-variables}

At each iteration, several calculations will be performed, the results of which will be saved in variables. These variables will be useful further for textually displaying the results of calculations.

```latex
\pgfmathsetmacro\rollAngleDeg{deg(\rollAngle)}
\pgfmathsetmacro\arcLength{0.5 * \rollAngle * \radius^2}
\pgfmathsetmacro\curvature{1 / (\radius * \rollAngle)}
```

The first variable `rollAngleDeg` will contain the value of the roll angle expressed in degrees.

Later we'll save the length of the involute arc in the `arcLength` variable. It has the following formula:

$$
L = \frac{1}{2} \psi r^2
$$

Finally, we'll calculate the curvature and save its value in the `curvature` variable. The formula for calculating it is as follows:

$$
\kappa = \frac{1}{\psi r}
$$

### 5.3. General settings of the plot axes of each frame {#general-frame-settings}

The environmental statement `\begin {axis}` and `\end {axis}` will set the correct scaling of the plot. We'll use simple linear scaling, but this package also has [other types](https://www.overleaf.com/learn/latex/pgfplots_package#Reference_guide) of scaling, which you can use to drawing other plots.

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

As we can see, the axes have a number of assigned options. We'll briefly develop their significance and usefulness.

{{< image src="/images/2020/08/latex-involute-of-a-circle/involute-demo1.png" alt="Plot with axes located in the center, linear scaling." caption="Plot with axes located in the center, linear scaling.">}}

#### 5.3.1. The *name* option {#name-option}

The `name` option sets the name of the plot. This option will allow us, accessing the graph by name, to position to its right an information box with all the calculations of the involute at each iteration.

#### 5.3.2. The *trig format=rad* option {#rad-format-option}

By default, the `pgfplots` package operates with degrees, when we have calculations containing trigonometric functions. This option allows to reconfigure the input format for trigonometric functions like `sin`, `cos`, `tan`, and their friends [^pgfplots-ctan-56].

#### 5.3.3. The *axis equal* option {#axis-option}

With the `axis equal` option, each unit vector is set to the same length while the axis dimensions stay constant. Afterwards, the size ratios for each unit in `x` and `y` will be the same. Axis limits will be enlarged to compensate for the scaling effect [^pgfplots-ctan-298].

#### 5.3.4. The *axis lines=center* option {#axis-lines-option}

By default the axis lines are drawn as a box, but it is possible to change the appearance of the `x-axis` and `y-axis` lines. Assigning a value from those available will allow to choose the locations of the axis lines  [^pgfplots-ctan-270-271].

We'll set the value center, which will mean that the axes will intersect at the coordinate `0` (origin).

#### 5.3.5. The *grid=both* option {#grid-option}

This option will draw the grid lines on the plot.

#### 5.3.6. The *xlabel* și *ylabel* options {#labels-options}

These options will draw the labels of the plot axes. The `$` symbol specifies that the text is a LaTeX mathematical formula.

#### 5.3.7. The *xmin*, *xmax*, *ymin* și *ymax* options {#plot-limits-options}

These options allow to define the axis limits, i.e. the lower left and the upper right corner. Everything outside of the axis limits will be clipped away [^pgfplots-ctan-327].

#### 5.3.8. The *xticklabels* și *yticklabels* options {#tick-labels-options}

Aceste opțiuni permit atribuirea etichetelor pentru fiecare pas a axei (segmente ale axelor). În cazul nostru, nu avem nevoie de etichetele cu numerotarea fiecărui segment al axelor. Pentru aceasta, vom seta la aceste opțiuni valoarea `\empty` (gol).

These options allow to assign labels for each axis step (axis segments). In our case, we do not need the labels with the numbering of each segment of the axes. To do this, we'll set the `\empty` value to these options.

### 5.4. Adding the necessary coordinates to the plots {#coordonates}

Next, we'll add 3 coordinates on the plot, namely $O$, $L_{\tiny 1}$ and $L_{\tiny 2}$. These coordinates will allow us to draw segments.

The syntax for adding the coordinate to the plot is as follows:

`\coordonate[<options>] (<name>) at (<coordonate>);`

So the coordinates $O$, $L_{\tiny 1}$ and $L_{\tiny 2}$ will be added as follows:

```latex
\coordonate (O) at (0,0);
\coordonate (L1) at ({arcx(\radius,0,\rollAngle)},{arcy(\radius,0,\rollAngle)});
\coordonate (L2) at ({involutex(\radius,\rollAngle)},{involutey(\radius,\rollAngle)});
```

The $OL_{\tiny 1}$ segment will represent the radius of the circle, and the angle between this segment and the segment $[0,r]$ will be the rolling angle itself.

The $L_{\tiny 1}L_{\tiny 2}$ segment will represent the tangent of the circle, starting from the perpendicular to the maximum point of the involute (calculating the values ​​of the parametric equations, where $\psi$ will be equal to the current value of the variable `\rollAngle`).

{{< image src="/images/2020/08/latex-involute-of-a-circle/involute-demo-coords.png" alt="The O, L1 și L2 coordinates." caption="The $O$, $L_1$ și $L_2$ coordinates.">}}

### 5.5. Drawing the remaining arc of the circle after "debugging the thread from the spool" {#remaining-arc-circle-plot}

Because we mentioned that the involute can be represented as the debugging of the thread from the spool, then at each iteration we'll eliminate a part of the circle that corresponds to the angle `\rollAngle`.

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/08/latex-involute-of-a-circle/involute-demo2.mp4" type="video/mp4">
    </video>
    <figcaption>The remaining arc of the circle.</figcaption>
</figure>

The `\addplot` command is the main plotting command, available within each axis environment. It can be used one or more times within an axis to add plots to the current axis  [^pgfplots-ctan-43].

The syntax for adding the plot to the axes is as follows:

```latex
\addplot[<options>] <input data> <trailing path commands>;
```

So, to build the plot with the remaining arc of a circle after debugging, we'll write the following command:

```latex
\addplot [domain=2*pi:\rollAngle,samples=200,remainingArcColor,thick,line cap=round]({arcx(\radius,0,x)},{arcy(\radius,0,x)});
```

We'll continue to develop the options set when building the plot, with the exception of `remainingArcColor`. This option only sets the color of the plot line with that declared in one of the [previous sections](#colors).

#### 5.5.1. The *domain* option {#domain-option}

This option sets the function's domain(s). Two dimensional plot expressions are defined as functions $f: [x_{\tiny 1},x_{\tiny 2}] \to \mathbb{R}$ and $\langle x_{\tiny 1} \rangle$ and $\langle x_{\tiny 2} \rangle$ [^pgfplots-ctan-55].

În cazul nostru, domeniul de definiție este $f: [2\pi:\psi] \to \mathbb{R}$, unde $\psi$ este unghiul curent de depanare, egal cu valoarea variabilei `\rollAngle`.

In our case, the function's domain is $f: [2\pi:\psi] \to \mathbb{R}$, where $\psi$ is the current roll angle. In other words, from iteration to iteration the circle will lose some part of it. The angle of the circle arc removed from the circle will correspond to the value `\rollAngle`.

#### 5.5.2. The *samples* option {#samples-option}

The `samples` option Sets the number of sample points [^pgfplots-ctan-56]. It should be noted that these withdrawals will be within the previously defined function's domain.

#### 5.5.3. The *thick* option {#thick-option}

This option allows to set the width of the plot line. The `thick` option corresponds to `0.8pt` line width [^tikz-wikibooks-line-width].

TikZ offers predefined line widths as follows [^pgfplots-ctan-190]:

* thin
* ultra thin
* very thin
* semithick
* thick
* very thick
* ultra thick

#### 5.5.4. The *line cap* option {#line-cap-option}

This option specifies how lines "end". Permissible type are `round`, `rect` and `butt`. They have the following
effects [^tikz-ctan-175]:

{{< image src="/images/2020/08/latex-involute-of-a-circle/tikz-line-cap.png" alt="Types of line terminations." caption="Types of line terminations. Credits:  [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/base/doc/pgfmanual.pdf)">}}

For the graphical representation of all parametric equations, we'll use rounded line cap, in other words we'll use the `line cap=round` option.

Similarly, with these described options, we'll build the other graphics.

### 5.6. Drawing the debugged arc of a circle {#remaining-arc-of-circle-plotting}

By the command below, we'll build at each iteration a dotted arc of a circle (the `dashedLineColor` option), which will represent the roll angle of the involute on the circle.

This arc of a circle will have the domain exactly the opposite of the previous one, ie $f: [0:\psi] \to \mathbb{R}$.

```latex
\addplot [domain=0:\rollAngle,samples=200,dashedLineColor,dashed,line cap=round]({arcx(\radius,0,x)},{arcy(\radius,0,x)});
```

As a result, visually we'll have a single circle that actually consists of two opposite arcs of a circle, with different colors and styles.

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/08/latex-involute-of-a-circle/involute-demo3.mp4" type="video/mp4">
    </video>
    <figcaption>Drawing the arc of the circle.</figcaption>
</figure>

### 5.7. Drawing the involute curve {#involute-plotting}

Iată am ajuns și la cel mai important punct. Aici vom construi evolventa propriu-zisă. La construirea acesteia vom folosi ecuațiile parametrice discutate anterior [anterior](#tikzpicture).

Here we come to the most important point. Here we'll draw the actual involute curve. When constructing it, we'll use the parametric equations described [above](#tikzpicture).

```latex
\addplot [domain=0.01:\rollAngle,samples=200,involuteSplineColor,thick,line cap=round]({involutex(\radius,x)},{involutey(\radius,x)});
```

As a result, we obtain the involute profile:

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/08/latex-involute-of-a-circle/involute-demo4.mp4" type="video/mp4">
    </video>
    <figcaption>Drawing the involute profile on the plot.</figcaption>
</figure>

### 5.8. Drawing the line that joins the tangent to the end of the involute curve {#line-plotting}

The next step will be drawing the line that that joins the tangent to the end of the involute curve.

We'll do this with the help of the `\draw` command. This line will have the color assigned in the `tangentLineColor` variable, the width of the line will be of `thick` type and will have the coordinates `L1` and `L2` which we have declared and initialized in one of the [previous sections](#coordonates).

```latex
\draw[tangentLineColor,thick] (L1) -- (L2);
```

This line will represent that "thread" that we untie from the spool 🧵. The result looks like this:

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/08/latex-involute-of-a-circle/involute-demo5.mp4" type="video/mp4">
    </video>
    <figcaption>The line joining the tangent to the end of the involute.</figcaption>
</figure>

### 5.9. Drawing the circle radius line {#radius-line-plotting}

Also with the same syntax we'll design the radius of the circle that will rotate as the roll angle increases.

```latex
\draw[dashedLineColor,dashed] (O) -- (L1) node [accentColor,pos=0.5,sloped,above] {$r$};
```

We can see the result in the animation below, but the options we set at the node, we'll develop in the following sections.

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/08/latex-involute-of-a-circle/involute-demo6.mp4" type="video/mp4">
    </video>
    <figcaption>Drawing the circle radius.</figcaption>
</figure>

#### 5.9.1. The */tikz/pos* option {#pos-option}

When the `/tikz/pos=<fraction>` option is given, the node is not anchored on the last coordinate. Rather, it is anchored on some point on the line from the previous coordinate to the current point. The `<fraction>` dictates how "far" on the line the point should be. A `<fraction>` of `0` is the previous coordinate, `1` is the current one, everything else is in between. In particular, `0.5` is the middle [^tikz-ctan-246].

We'll set the value `0.5`, which will mean that the node will be in the middle of the line. We can do the same with the `/tikz/midway` option, which is the equivalent of the option `pos=0.5`.

#### 5.9.2. The */tikz/sloped* option {#slopped-option}

The `/tikz/sloped` option causes the node to be rotated such that a horizontal line becomes a tangent to the curve. The rotation is normally done in such a way that text is never "upside down" [^tikz-ctan-248].

{{< image src="/images/2020/08/latex-involute-of-a-circle/tikz-sloped.png" alt="The slopped option of the TikZ package." caption="The `/tikz/sloped` option of the TikZ package. Credits:  [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/base/doc/pgfmanual.pdf)">}}

In our case we have not a curve, but a line and the text must rotate as the line rotates. At the time when the roll angle will exceed $\frac{\pi}{2}$ radians or $90^{\circ}$, this option will not allow the text to be inverted (upside down).

#### 5.9.3. The */tikz/above* option {#above-option}

This option is equivalent to the option `/tikz/anchor=south` and allows the node to be positioned above the line.

### 5.10. Drawing the angle of the debugged arc {#involute-angle-plotting}

La această etapă, vom proiecta unghiul arcului de cerc depanat. Pentru aceasta, vom utiliza comanda `\addplot`, sintaxa căreia am desfășurat-o în una din [secțiunile anterioare](#remaining-arc-circle-plot). Unica diferență este că aici adăugăm un nod fix poziționat în punctul $(0.5,-0.3)$ cu textul $\psi$.

At this stage, we'll project the angle of the debugged arc. For this, we'll use the command `\addplot`, the syntax of which we developed in one of the [previous sections](#remaining-arc-circle-plot). The only difference is that here we add a fixed node positioned in the $(0.5,-0.3)$ point with the $\psi$ text.

```latex
\addplot [domain=0:\rollAngle,samples=200,accentColor,line cap=round]({arcx(.4,0,x)},{arcy(.4,0,x)}) node[] at (.5, -.3) {$\psi$};
```

Of course, $\LaTeX$ has a wide range of packages for drawing angles (such as the [tkz-euclide](http://ctan.mirror.ftn.uns.ac.rs/macros/latex/contrib/tkz/tkz-euclide/doc/TKZdoc-euclide.pdf) package), but we'll go the way of designing the same arc of a circle, only with a smaller radius.

<figure>
    <video controls style="width: 70%;max-height: 100%;">
        <source src="/images/2020/08/latex-involute-of-a-circle/involute-demo7.mp4" type="video/mp4">
    </video>
    <figcaption>Drawing the roll angle of the involute.</figcaption>
</figure>

### 5.11. Drawing the involute parameters at each iteration {#involute-parameters-drawing}

The involute parameters at each iteration will be positioned in a box, the last one being positioned to the right of our plot.

<figure>
    <video controls autoplay style="width: 100%;max-height: 100%;">
        <source src="/images/2020/08/latex-involute-of-a-circle/involute-demo8.mp4" type="video/mp4">
    </video>
    <figcaption>Displaying the involute parameters at each iteration.</figcaption>
</figure>

The box code with the involute parameters can be seen below:

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

This piece of code at first glance seems difficult. In the following sections we'll explain some key moments that take place in this fragment of code.

#### 5.11.1. The *\node* command {#node-command}

Nodes are probably the most universal elements in `TikZ`. A node is usually a rectangle or circle or other simple shape with text on it. In the simplest case, a node is just text that is placed at a certain coordinate.

```latex
\node[<options>](<name>) at (<coordinate>){<text>};
```

In our case, we'll create a node with the coordinate located in the upper right corner of the main plot. This is done by referring to the axis name of the main plot, with indicating the anchor in the northeast position.

{{< image src="/images/2020/08/latex-involute-of-a-circle/tikz-anchor-point.png" alt="Anchors positioned on the axis delimitation box in the TikZ package." caption="Anchors positioned on the axis delimitation box in the TikZ package. Credits: [CTAN](http://ctan.mirror.ftn.uns.ac.rs/graphics/pgf/contrib/pgfplots/doc/pgfplots.pdf)">}}

The `xshift=.5cm` option allows you to perform the translation of the box on the axis $x$ with `0.5cm`, `below right` -- positioning the box to the right below the previously set coordinate and taking into account the translation performed.

The `/tikz/text width=6cm` option will place the node text in a `6cm` width box. If the width of the text exceeds this limit, then the line will be breaked and the remaining content will be placed in a new line.

În ceea ce privește opțiunea `/tikz/align=center`, aceasta este utilizată pentru a configura alinierea textului cu mai multe linii în interiorul unui nod. Dacă opțiunea `/tikz/text width` este setată la o anumită lățime (să numim această aliniere cu line breaking), opțiunea de aliniere va configura `\leftskip` și `\rightskip` în așa fel încât textul să fie întrerupt și aliniat în funcție de opțiunea de aliniere [^tikz-ctan-235].

As for the `/tikz/align=center` option, it is used to set up an alignment for multi-line text inside a node. If text width is set to some width (let us call this alignment with line breaking), the align key will setup the `\leftskip` and the `\rightskip` in such a way that the text is broken and aligned according to `alignment option` [^tikz-ctan-235].

The `style=information text` option allows you to set the style that we identified [in one of the previous sections](#colors). This box with the involute parameters at each iteration will have a red background color with 10% intensity of the base color.

#### 5.11.2. Display color text {#colored-text-drawing}

To display a color text in the node, we can use the below syntax, the color names being identified [in the first sections](#colors).

```latex
{\color{accentColor} some text}
```

#### 5.11.3. Aligning the mathematical formulas in the box {#formulas-drawing}

Mathematical formulas will not be aligned in a simple form (left, center, right), but will have a complex shape. The alignment will be done to the `=` symbol, in other words all the 4 formulas will be positioned one below the other with the strict alignment to this symbol.

{{< image src="/images/2020/08/latex-involute-of-a-circle/involute-demo-text-align.png" alt="Aligning mathematical formulas at the equal symbol." caption="Aligning mathematical formulas at the equal symbol.">}}

This is done with the help of the `amsmath` package, using the construction `\begin{align*} ... \end{align*}` and determining by the symbol `&` where we need to align the equation.

```latex
\begin{align*}
    {\color{accentColor} r} & = const & & = \radius \\
\end{align*}
```

You can read [here](https://tex.stackexchange.com/a/159724) about the meaning and usefulness of the `&` symbol.

## 6. Production of the final output file with the involute animation {#file-output-and-animation}

Given that we are working in `devContainer`, we already have all the packages installed to convert the `pdf` file to `mp4` (video file with involute animation). It was possible to convert to a `gif` file, but this format is outdated and has [a number of disadvantages](https://connectusfund.org/6-advantages-and-disadvantages-of-animated-gifs).

There are a lot of better formats such as [webp](https://en.wikipedia.org/wiki/WebP), [apng](https://en.wikipedia.org/wiki/APNG) and so on. We'll not use these formats, because the problem is compatibility. These formats are not fully supported by all browsers (i.e. [apng](https://caniuse.com/#feat=apng), [webp](https://caniuse.com/#feat=webp)).

The best option is `mp4`. This format and its `H.264` codec is supported by [all browsers](https://caniuse.com/#feat=mpeg4). We can also set the [loop](https://www.geeksforgeeks.org/html-video-loop-attribute/) option for cyclic repetition of the video and thus we'll get the same effect as in the case of a `gif` file type.

The first step is to convert the generated `pdf` file into a sequence of images using the [GraphicsMagick](http://www.graphicsmagick.org/). In other words, each page in the file will be saved in separate images with the `png` extension.

To perform this conversion, we'll use the command below, which will save sequences of images with `300 DPI` density and white background.

```bash
mkdir involute-of-circle/output/

gm convert -density 300 involute-of-circle/involute-demo.pdf -background white +adjoin involute-of-circle/output/image_%02d.png

# Alternativă folosind pachetul Ghostscript
gs -sDEVICE=pngalpha -o involute-of-circle/output/image_%02d.png -r300 involute-of-circle/involute-demo.pdf
```

The next step is converting the image sequence to a `mp4` video file type. For this, we'll use the pre-installed package in the container called [FFmpeg](https://ffmpeg.org/).

```bash
ffmpeg -r 15 -i involute-of-circle/output/image_%02d.png -c:v libx264 -vf fps=60 -pix_fmt yuv420p -vf "pad=ceil(iw/2)*2:ceil(ih/2)*2" involute-of-circle/output/out.mp4
```

## 7. Conclusion {#conclusion}

$\LaTeX$ is an advanced document preparation system. It has a large number of packages that allow you to perform complex tasks.

Animarea evolventei am realizat-o cu ajutorul ciclului `foreach`, unde la fiecare iterație am modificat unghiul de depanare. Ca rezultat am obținut un fișier `pdf` cu cadrele necesare pentru animare. Ulterior, acest fișier l-am convertit în fișier `mp4` cu ajutorul pachetului [GraphicsMagick](http://www.graphicsmagick.org/).

In this article we used the `PGFPlots` package (which in turn uses the `TikZ` package) to draw the involute of a circle.

We animated the involute with the help of the foreach loop, where at each iteration we changed the roll angle. As a result, we obtained a `pdf` file with the necessary frames for animation. Later, we converted this file to a `mp4` file using the [GraphicsMagick](http://www.graphicsmagick.org/) package.

<figure>
    <video controls autoplay style="width: 100%;max-height: 100%;">
        <source src="/images/2020/08/latex-involute-of-a-circle/involute-demo8.mp4" type="video/mp4">
    </video>
    <figcaption>The final result.</figcaption>
</figure>

Experimenting with evolving, we can obtain these figures:

{{< image src="/images/2020/08/latex-involute-of-a-circle/involute-demo-multiple-7-8.png" alt="Involute experiments." caption="Involute experiments.">}}

The full code is on the [Github repository](https://github.com/sunt-programator/latex-workpapers).

{{< admonition type=tip title="Disclaimer" >}}
The representative image of this article contains graphic elements taken from [Freepik (design by macrovector)](http://www.freepik.com).
{{< /admonition >}}

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
[^parametric-equation-wiki]: Parametric equation. Credits: [Wikipedia](https://en.wikipedia.org/wiki/Parametric_equation)
[^tikz-wikibooks-line-width]: LaTeX/PGF/TikZ. Line width. Credits: [Wikibooks](https://en.wikibooks.org/wiki/LaTeX/PGF/TikZ#Line_width)
[^dockerfile-reference]: Dockerfile reference. Credits: [docs.docker.com](https://docs.docker.com/engine/reference/builder/)
[^tex-friends]: What are TEX and its friends? Credits: [CTAN](https://www.ctan.org/tex)
