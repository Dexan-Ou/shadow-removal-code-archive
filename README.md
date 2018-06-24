forked from Maciej Gryka's work.
# A simple instruction about how to run this code
I found this paper "[Learning to Remove Soft Shadows](http://visual.cs.ucl.ac.uk/pubs/softshadows/)" during my Computer-Graph class. And I decide to run this demo code to see what it can do. But just as Maciej Gryka said:

> Consequently, it will take quite a bit of work to get things to run, from fixing all the hard-coded paths to compiling the C++ code to generating the training data and figuring out the correct place to put it.

Okay, I finally make it. It is a bit plainful to make this code work, so I decide to write a simple instruction. Maybe it could give you some help.

## Generate trainning materials

You need to clone this repository [`shadow_gen`](https://github.com/maciejgryka/shadow-gen) to generate shadowed-unshadowed image pairs as trainning materials.
Then you will need these things to make it work:

### Requirement

* Autodesk Maya 2013+
* NVidia Mental Ray for Maya

### Instruction

* Install `Maya`(you can get a student pack if you are still student) and `Mental Ray Plugin`/`Mental Ray for Maya`. Besides, you do not need to install `pyMEL` as it is built-in since `Maya 2013`.
* Start `Maya`, 
* Create a empty scene, 
* Load the `Mental Ray Plugin`(`Mayatomr.mll`), 
* Open the `Render Settings`, change render to `mental ray` and change the output format to `PNG`. Then create `Physical Sun and Sky`.
* Delete the `sunShape` as we only need `physical sky`.
* Save the scene as `shadow-gen-master\base_white.mb`.
* Open the `Script Editor`(I am not using the English version, so it might be incorrect), change to `python` mode, load `shadow-gen-master\create_scene.py`.
* Change the `__FILE__` to absolutely path of `create_scene.py`, as we will run the code from editor(so there has no `__FILE__`). You might want to change the `n_images`, as it control the size of outputs.
* Run the code.

## Compiling the `PenumbraRemoval`

This part is the key of the whole work. It is a bit plainful to configure the environment. So, be patient.

### Requirement

* [OpenCV](https://opencv.org/) 2.4.1 *(seem some bugs in 2.3.1)*
* [Eigen](http://eigen.tuxfamily.org)
* [TRW-S v1.3](https://download.microsoft.com/download/6/E/D/6ED0E6CF-C06E-4D4E-9F70-C5932795CC12/TRW_S-v1.3.zip)
* Microsoft Visual Studio 2013+

### Instruction

* Use `Visual Studio` to open `shadow-removal-code-archive\PenumbraRemoval\PenumbraRemoval.sln`, it contains three VC projects, fix the include path and lib path of every projects.(but I think you only need to fix the `PenumbraRemoval\PenumbraRemoval`) Don't forget to fix the .cpp in `PenumbraRemoval\PenumbraRemoval`, it use three .cpp from `TRW-S`.

# Learning to Remove Soft Shadows - code archive
This repository contains the code for our 2015 ACM Transactions on Graphics paper "[Learning to Remove Soft Shadows](http://visual.cs.ucl.ac.uk/pubs/softshadows/)".

This is mostly for reference - it has been a long while since I ran this and I do not have access to the machine where everything used to be set up. Consequently, it will take quite a bit of work to get things to run, from fixing all the hard-coded paths to compiling the C++ code to generating the training data and figuring out the correct place to put it. While I probably won't be able to help with individual requests, here is the overall structure of how things worked:

- A complimentary repository [`shadow_gen`](https://github.com/maciejgryka/shadow-gen) contains the code used to generate the training data (shadowed-unshadowed image pairs).
- `PenumbraRemoval` is the core algorithm written in C++. It can do either training or evaluation using an already-trained model, depending on the options given. `PenumbraDataInspection` is a helper library and `RandomForest` contains an implementation of Mutlivariate Regression Random Forest that's used at the core of our shadow removal algorithm.
- `prem_integration` contain a bunch of glue code in Python that actually made things run and launch the compiled binaries described above. A good starting point for looking through it is `prem_integration/run.py`. A thing to note are the options files, `prem_options.txt` and `local_options.txt`, which control how things run (e.g. whether we're training or testing/evaluating), specify learning parameters such as number of trees in the Random Forest, their depth etc., as well as specify paths to where to find things. The paths in `prem_options.txt` are relative to whatever is defined in `local_options.txt`.
- Another interesting thing is a script to automate image inpainting using Adobe Photoshop's "context-aware fill" functionality: `prem_integration/inpaint_file.jsx`. This JavaScript file was turned into a corresponding `.exe`, which would then launch Photoshop itself and do the inpainting. If you want more details about this part, the [Photoshop JS Scripting Reference](https://wwwimages2.adobe.com/content/dam/acom/en/devnet/photoshop/scripting/Photoshop-CS6-JavaScript-Ref.pdf) is probably a good place to start.
- `experiments` is mostly dysfunctional - it was a place, where I evaluated the impact of the size of the training set on the final result; there might be some interesting things there (including some compiled binaries), so I've left it in.
- Finally, `Scribbler` contains a small Qt application that was used to create the binary shadow masks by painting over selected images.

I hope this is helpful - thanks for your interest in our work and best of luck in digging through this code and possibly even resurrecting it. If you manage to get something useful out of it - let me know!
