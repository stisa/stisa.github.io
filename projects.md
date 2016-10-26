---
layout: page
date: 2015-12-12T11:49:01+01:00
draft: false
title: "Projects"
permalink: /projects/
---
On this page I'll try to keep track of my ongoing projects.

## Mazes
This is a simple game, I built it while trying to understand depth-first search maze generators.  
Turns out the hardest part is knowing which cells are near each other.  
Built with luxe.  
  
[Source](https://github.com/stisa/mazes/) - [Demo](/mazes)  

## Jupyter Nim Kernel
A Pretty simple [Jupyter](http://jupyter.org/) kernel for [nim](http://nim-lang.org).  
There are two versions:

### INim
INim is an implementation of all the kernel machinery in [nim](http://nim-lang.org).
This is the version I will focus development on.

[Source](https://github.com/stisa/INim) - [Example](https://github.com/stisa/INim/blob/master/examples/example-notebook.ipynb)

### Jupyter-nim
This version calls the `python` implementation. It's written in `python`.

[Source](https://github.com/stisa/jupyter-nim-kernel) - [Example](https://github.com/stisa/jupyter-nim-kernel/blob/master/example-notebook.ipynb)  


PS: [This gist](https://gist.github.com/stisa/27b9d2f4aed9c85d7501ea38e15d9865) has a tool to convert a  notebook `.ipynb` file into  
a `.nim` file, keeping `markdown` blocks as `##[]##` blocks for documentation purposes.  

## Nim WebGL bindings
Simple bindings for webgl in [nim](http://nim-lang.org).

[Source](https://github.com/stisa/webgl) - [Examples](/webgl)

## NPng
A naive png encoder written in nim, that can also inline images when used on the js target.  
Currently does no compression at all, so expect **BIG** images.

[Source](https://github.com/stisa/npng) - [Docs](/npng) - [Examples](/npng/examples.html)

## Graph 
A toy plotter that creates `.png` files. Uses `npng`.

![An example](https://github.com/stisa/graph/raw/master/examples/example2.png)

[Source](https://github.com/stisa/graph) 

## SNAiL
A simple algebra library written in nim, that also works on the js target.
Not very fast (for now).

[Source](https://github.com/stisa/snail) - [Docs](/snail)