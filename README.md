# pslab

This repository contains documentation, and potentially some scripts, that
explain how our systems should be set up so that we can run the code developed
by our group.

## Python distribution and packages

In order to have a similar software environment, we rely on
[conda](http://conda.pydata.org/docs/)].
Please do a [full installation](http://conda.pydata.org/docs/install/full.html).

### Instructions for Purdue clusters
At the moment, the conda installation at conte.rcac.purdue.edu seems broken.
Install it from scratch, by:

1. Downloading the latest version of conda;
2. Running the installation script from the command line and answering yes
   to all questions.

### Install packages that are not in the standard distribution
We will need the following:

1. GPy:
   ```bash
   conda install pymc 
   ```
    

### Installing packages with pip from their github repository
This is a very useful command to remember in order to install non-standard 
packages in conda.
Do this
