October 18, 2016

[Notes](vid1.md) for video [1 - CEPL: Lisp, Textures and Shaders](https://www.youtube.com/watch?v=I0kWZP9L9Kc)

# vid1.step1.lisp
First of all, let's get to the [starting point of the video](https://www.youtube.com/watch?v=I0kWZP9L9Kc) and load the code to display a blue rectangle.

In Emacs, open "vid1.step1.lisp", and compile it with `C-c C-k`.

Comparing your source to the video you will note a few discrepancies.

- The demo is executed with `run-loop` and stopped with `stop-loop`, like all other demos.  `*running*` is used to track the current state - when `nil` the demo terminates.
- `*quad*` is initialized with 3D vectors, in compliance with [g-pt format](http://techsnuffle.com/cepl/api.html#CEPL.TYPES.PREDEFINED:G-PT).
- `vert-data` struct is no longer needed, as the data is in `g-pt` format.
- `defpipeline` is obsolete.  Separate shaders and `def-g` follow.
- `vert` shader takes a `g-pt` parameter.  The first (and only) value it returns is used by GL as the position.  As GL requires a 4D vector and `(pos vert)` provides 3 components, the forth one is appended here to always be 1.
- `frag` shader here takes no parameters.  It simply outputs blue (blue is the third component of the RGBA vector).
- [def-g->](http://techsnuffle.com/cepl/api.html#CEPL.PIPELINES:DEF-G-%3E) creates the GPU pipeline.
- `step-demo` looks a little different; the comments are self-explanatory.
- `run-loop` is modernized.  Note that `:element-type` is now `'g-pt`.
- `(with-viewport (make-viewport`... creates a new, square viewport.
- Note that the syntax for [make-c-array](http://techsnuffle.com/cepl/api.html#CEPL.C-ARRAYS:MAKE-C-ARRAY) and [make-gpu-array](http://techsnuffle.com/cepl/api.html#CEPL.GPU-ARRAYS.BUFFER-BACKED:MAKE-GPU-ARRAY) has changed - the first parameter is the data.

At this point you should be able to see the blue rectangle in the CEPL output window.

Remember to (stop-loop) before messing around with the shaders.  If redefining shaders with different parameters confuses CEPL, so you may need to M-x slime-restart-inferior-lisp  and restart CEPL [as described before](README.md).

# vid1.step2.lisp
#### [at approximately 1:56](https://youtu.be/I0kWZP9L9Kc?t=116) adding textures...

- In addition to `*tex-array*` and `*texture*` defparameters, you will note one for `*sampler*`
- The video describes how to initialize the texture in the REPL.  The code is inside run-loop.
- The `vert` shader now returns two values.  The first one is consumed by GL, and the second one is passed on to the next stage in the pipeline, the `frag` shader.  This second value now looks like (:smooth ([tex](http://techsnuffle.com/cepl/api.html#CEPL.TYPES.PREDEFINED:TEX) vert)) instead of the gobbledegoo in the video.
- The `frag` shader now takes parameters: data from the previous stage, and a uniform sampled texture.  Note that we now return the color using the `texture` function that operates on the parameters named `texture` and `tex-coord`.
- [map-g](http://techsnuffle.com/cepl/api.html#CEPL.PIPELINES:MAP-G) now specifies the sampled texture for the pipeline.
- `run-loop` initialization looks a little different from the video to match current reality.

Note: Compiling vid1.step2.lisp after using vis1.step.lisp will result in an error, as CEPL does not know which of the GPU functions to select.  This can be resolved by specifying parameter types for the frag shader explicitly.  But for now it's easier to M-x restart-inferior-lisp and reload CEPL cleanly before compiling this step...

You should now see a bumpy red rectangle.


