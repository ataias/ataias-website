+++
date = "2016-03-03T16:28:42-03:00"
tags = ["julialang", "python", "hdf5"]
title = "How to write an HDF5 file in Julia and read it using Python"

+++

In a numerical simulation project I have been working with, I write most of my data in binary files using `read` and `write`. This is a simple approach, but it doesn't scale well. My project got bigger and I have been getting constantly irritated with the fact that it is cumbersome to read the data I saved. For instance, check this code:

<pre>
<code class="language-python">
#Python code
for i in range(0, numberFrames):
            time = i/fps

            #Read matrices of size n by n
            readMatrix(u, f, n)
            readMatrix(v, f, n)

            # some code to plot data
            # (...)

            #I am not plotting this specific matrix, I need to jump over it
            f.seek(n*n*8*1, 1) #jump matrix

            #Reads four matrices
            readMatrix(Hx, f, n)
            readMatrix(Hy, f, n)
            readMatrix(Mx, f, n)
            readMatrix(My, f, n)

            #Jumps other matrices not being used
            f.seek(n*n*8*4, 1)
</code>
</pre>

What happens if I add a new piece of data? I will have to change this code, even if it doesn't use the new information. That's because a line will be added or edited just to jump over some bytes. If I were using a file format like `HDF5`, I wouldn't need to worry about data that is there and won't be used.

In my case, I write data in Julia as it is faster to write for-loops and perform simulations, but I read it in Python as I am using matplotlib to plot data. In Julia, I need to install HDF5 with the command <kbd>Pkg.add("HDF5")</kbd> called inside Julia. As for Python, I installed HDF5 running <kbd>pip install h5py</kbd> in the terminal. I wrote a code testing writing in Julia:

<pre>
<code class="language-julia">
#Julia code
using HDF5

A = [1 2 3; 2 3 4]
B = [3 4 5; 6 7 8]
C = [3 4 5; 6 8 8]

file = h5open("./test.h5", "w")

frames = g_create(file, "frames")

frames["1"] = A
frames["2"] = B
frames["3"] = C

attrs(frames)["Description"] = "A test"

close(file)
</code>
</pre>

The fact that I am able to add attributes to describe my data is awesome. I didn't support this feature when writing directly in binary as I thought it would be too much trouble but as it is already there in `HDF5`, I can use it without writing more code to deal with the file. As for Python, I can read the attributes and data using the sample code below:

<pre>
<code class="language-python">
#!/usr/bin/env python3
import h5py
import numpy as np

f = h5py.File("test.h5", "r")

#Read matrix

A = f['frames/1']

#Read attributes
g = f.get("frames")
description = g.attrs["Description"]

f.close()

</code>
</pre>

I hope this helps anyone willing to do something similar. If any of the code above doesn't work in the future that you are reading it, let me know!
