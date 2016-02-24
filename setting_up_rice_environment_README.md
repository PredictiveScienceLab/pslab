SETTING UP RICE ENVIRONMENT
===========================
> Prepared by Ilias Bilionis (ibilion@purdue.edu) on 02/23/2016

This document describes how the members of the Predictive Science Lab should set up their environment on ``rice.rcac.purdue.edu``. This is mainly to facilitate the compilation of existing software and the developments of new one.

Directory structure
-------------------
You must create the following directory structure:
+ ``$HOME/ext``: This is where we will be installing new software. The environment variable ``$PREFIX`` will be set to point to this directory.
+ ``$HOME/ext/src``: We will be putting source code for compilation here.

So, all you must run is:
```
mkdir -p $HOME/ext/src
```

``$PREFIX`` will be populated gradually as you start to install new software. For future reference, this is where things will go at the end:
+ ``$PREFIX/bin``: Compiled binaries will go here. This must be added to your ``$PATH``.
+ ``$PREFIX/lib``: Compiled libraries will go here. This must be added to your ``$LD_LIBRARY_PATH``.
+ ``$PREFIX/lib/python2.7/site-packages``: This is where Python packages will go. This must be added to your ``$PYTHONPATH``.

Your ``$HOME/.profile``
----------------------
Your ``$HOME/.profile`` contains shell commands that are run every time a new shell starts. Add to it the following lines:
```
module load anaconda
export PREFIX=$HOME/ext
export PATH=$PREFIX/bin:$PATH
export LD_LIBRARY_PATH=$PREFIX/lib:$LD_LIBRARY_PATH
export PYTHONPATH=$PREFIX/lib/python2.7/site-packages:$PYTHONPATH
```