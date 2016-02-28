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

After that, we need to begin using Tikz. Inside the curly braces, code is added with initial setup:

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
