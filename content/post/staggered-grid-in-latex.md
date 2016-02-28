+++
date = "2016-02-28T10:39:39-03:00"
tags = ["latex", "tikz"]
title = "Creating a staggered grid in latex with TikZ"
+++

A staggered grid has some data points in different locations but they are saved in the same place. Weird? Maybe! Nevertheless, it helps with spurious modes that appear in mathematical simulations with finite differences of the Navier Stokes equations and maybe other. Even if you are not interested in representing a staggered grid, this tutorial may serve you well to learn some Tikz. First off, create a new file named `pgraphics.sty` in the directory that you will save your `.tex` file latex. We start by creating a package named `pgraphics` that will be later reference in the `.tex` file. After that, we can write a command that will be than used whenever we want to place the grid. Now, the file looks like:

<pre>
<code class="language-tex">
\ProvidesPackage{pgraphics}

\newcommand{\domainOne}[0]{
}
</code>
</pre>

After that, we need to begin using TikZ. Inside the curly braces, code is added with initial setup:

<pre>
<code class="language-tex">
\begin{center}

%grid itself has dashed lines here
\tikzstyle{help lines}+=[dashed]

\begin{tikzpicture}

%Order is important!
%If switched those lines,
%dashed grey lines will appear
%on top of black lines

%outer grid
\draw[style=help lines] (-1,-1) grid +(7,7);

%inner grid
\draw (0,0) grid +(5,5);

\end{tikzpicture}
\end{center}
</code>
</pre>

To test the result so far, we need to create a `document.tex` file with the content:

<pre>
<code class="language-tex">
\documentclass[a4paper,11pt]{article}

%basic packages
\usepackage[T1]{fontenc}
\usepackage[utf8]{inputenc}
\usepackage{lmodern}

%needed to use tikz
\usepackage{tikz}

%our custom package
\usepackage[]{pgraphics}

\begin{document}
\domainOne
\end{document}

</code>
</pre>

This file will not be changed. We just need to update the `pgraphics.sty` file and compile this `document.tex` file. On a terminal, type
<pre>
<code class="language-bash">
$ pdflatex document.tex
</code>
</pre>

At this stage, the result is {{< figure-responsive alt="Couldn't load Staggered grid" src="/assets/post/staggered-grid-in-latex/stage1.png" title="Staggered Grid - Stage 1" >}}

Now, we can draw the `x` and `y` axes with

<pre>
<code class="language-tex">
%axes
\draw[->] (0,5) -- (xyz cs:y=6);
\draw (0,6.3) node {$y(j)$};
\draw[->] (5,0) -- (xyz cs:x=6);
\draw (6.5,0) node {$x(i)$};
\draw (-0.18,-0.18) node {$O$};
</code>
</pre>

The first of these commands says to draw a line with an arrow starting in `y=5` and ending in `y=6`. The second just adds a label with the axis name at point `x=0`, `y=6.3`. The remaining lines are similar.

{{< figure-responsive alt="Couldn't load Staggered grid" src="/assets/post/staggered-grid-in-latex/stage2.png" title="Staggered Grid - Stage 2" >}}

So far there was no need to use a loop. The grid is done, but now we want to show exactly where the points are being saved. In my case, pressure values should be save in the middle of every cell. I don't want to put a circle in each of the squares manually, so I am going to use a `foreach` structure, like this

<pre>
<code class="language-tex">
%internal p points
\foreach \x in {0.5,1.5,2.5,3.5,4.5}
  \foreach \y in {0.5,1.5,2.5,3.5,4.5}
  {
  \fill (canvas cs:x=\x cm,y=\y cm) circle (2pt);
}

%External p points
\foreach \x in {0.5,1.5,2.5,3.5,4.5}
  \draw (\x,-0.5) circle (2pt);
\foreach \y in {0.5,1.5,2.5,3.5,4.5}
  \draw (5.5,\y) circle (2pt);
\foreach \y in {0.5,1.5,2.5,3.5,4.5}
  \draw (-0.5,\y) circle (2pt);
\foreach \x in {0.5,1.5,2.5,3.5,4.5}
  \draw (\x,5.5) circle (2pt);
</code>
</pre>

In the code above, an internal point is drawn with a filled circle and an outer point with an empty circle. Maybe there are more intelligent ways to do this, parameterizing the spacing between cells and a variable that indicates the last cell of the grid. I haven't tried to do this yet, but if you are interested we can talk about it.

{{< figure-responsive alt="Couldn't load Staggered grid" src="/assets/post/staggered-grid-in-latex/stage3.png" title="Staggered Grid - Stage 3" >}}

I want to plot velocities in this grid also. They should be plotted in the middle of the grid lines, instead of in the middle of the cells. To do that, I did

<code>
<pre class="language-tex">
%Internal Horizontal Velocities Points
\foreach \x in {-1.2,-0.2,0.8,1.8,2.8,3.8,4.8}
  \foreach \y in {0.5,1.5,2.5,3.5,4.5}
  {
  \draw[->] (\x,\y) -- (\x+0.4,\y);
}
%External Horizontal Velocities Points
\foreach \x in {-.2,0.8,1.8,2.8,3.8,4.8}
  \draw[->] (\x,5.5) -- (\x+0.4,5.5);
\foreach \x in {-.2,0.8,1.8,2.8,3.8,4.8}
  \draw[->] (\x,-.5) -- (\x+0.4,-.5);
%Internal Vertical Velocities Points
\foreach \x in {0.5,1.5,2.5,3.5,4.5}
  \foreach \y in {-1.2,-0.2,0.8,1.8,2.8,3.8,4.8}
  {
  \draw[->] (\x,\y) -- (\x,\y+0.4);
}
%External Vertical Velocities Points
\foreach \y in {-0.2,0.8,1.8,2.8,3.8,4.8}
  \draw[->] (5.5,\y) -- (5.5,\y+0.4);
\foreach \y in {-0.2,0.8,1.8,2.8,3.8,4.8}
  \draw[->] (-.5,\y) -- (-.5,\y+0.4);
</pre>
</code>

{{< figure-responsive alt="Couldn't load Staggered grid" src="/assets/post/staggered-grid-in-latex/stage4.png" title="Staggered Grid - Stage 4" >}}

That's all! You can see a gist with the final code [here](https://gist.github.com/ataias/ec06920bcb1a3c19b832).
