# nuDeep
Welcome to the nuTonomy repository for deep learning! 

## Overview
- [Installation](#installation)
- [Documentation](#documentation)
- [Testing](#testing)
- [Data](#data)


## Installation
nuDeep is a Python repo and we are currently using version `python 3.7.0`.


### Automatic installation
If you are doing a fresh install, you can try [the automatic Ubuntu installation script](https://github.com/nutonomy/nuDeep/blob/master/misc/cluster_management/install_nudeep.sh).
1) Change your name and email address at the top of the script.
2) Run it once and copy the SSH key to the Github homepage.
3) Rerun the script.

Else, follow the step-by-step instructions below.


### Install pip
```
sudo easy_install pip
```
### Install python

Ubuntu: If the right Python version isn't already installed on your system, install it by doing
```
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get install python3.7
sudo apt-get install python3.7-dev
```
Mac OS: Download from `https://www.python.org/downloads/mac-osx/` and install.


### Install virtualenvwrapper
```
pip install virtualenvwrapper
```
Add these two lines to `~/.bashrc` (`~/.bash_profile` on Mac OS) to set the location where the virtual environments should live, the location of your development project directories, and the location of the script installed with this package:
```
export WORKON_HOME=$HOME/.virtualenvs
source [VIRTUAL_ENV_LOCATION]
```
Replace `[VIRTUAL_ENV_LOCATION]` with either `/usr/local/bin/virtualenvwrapper.sh` or `~/.local/bin/virtualenvwrapper.sh` depending on where it is installed on your system.

After editing it, reload the shell startup file by running e.g. `source ~/.bashrc`.

### Create the virtual environment
```
mkvirtualenv nuenv --python [PYTHON_BINARIES] 
```
PYTHON_BINARIES are typically in either `/usr/local/bin/python3.7` or `/usr/bin/python3.7`.

Switch to the newly created virtual environment:
```
workon nuenv
```

### Setup PYTHONPATH

If no nuCore or other python versions exist or will be installed in the PC, you can just add the nuDeep directory to your PYTHONPATH by adding the following lines to `~/.bashrc` (`~/.bash_profile` on Mac OS):
```
export PYTHONPATH="${PYTHONPATH}:$HOME/nuDeep"
```

If you want to use nuCore (python 2.7.x) or install other python versions outside do the following 2 steps:

Add following 2 lines to the end of `$HOME/.virtualenvs/nuenv/bin/postactivate` .
```
export OLD_PYTHONPATH="$PYTHONPATH"
PYTHONPATH=/usr/local/lib/python3.7/dist-packages:$HOME/nuDeep:
```
Add following line to the end of `$HOME/.virtualenvs/nuenv/bin/postdeactivate`
```
export PYTHONPATH="$OLD_PYTHONPATH"
```

### Install Python packages
Check the CUDA version installed on your system using `cat /usr/local/cuda/version.txt`.

If you have CUDA 9.2 installed on your system, install the required packages using:
```
pip install -r requirements/cuda9.txt
```

If you have CUDA 8 installed on your system, install the required packages using:
```
pip install -r requirements/cuda8.txt
```

For CPU-only install, which is currently the only option available on Mac OS, use:
```
pip install -r requirements/cpu.txt
```

To hide matplotlib plots on a server environment, create a file `~/.config/matplotlib/matplotlibrc` and add the following code to it:
```
backend: Agg
```
Alternatively, install tkiter on Ubuntu via `sudo apt-get install python3.6-tk` and set the backend to `backend: TkAgg`.

### Config

To prevent the PyTorch dataloaders from crashing, it is recommended to add the following to your `~/.bashrc`:
```
bash
ulimit -n 20000
```
or another large number (the default on Ubuntu is ```ulimit -n 1024```).

### Trouble-shooting
* If the virtualenvwrapper installation fails because of the `six` package, ignore the installed `six` using:
```
pip install virtualenvwrapper --ignore-installed six
```

* If, during `pip install`, the package `pyquaternion` will not install, remove it from `requirements/base.txt` and install the rest of the requirements and then run:
```
pip install git+https://github.com/KieranWynn/pyquaternion
```

* On Mac OSX, if the package `shapely` will not install and you receive the following error - `OSError: Could not find library geos_c or load any of its variants`,
you will need to install `geos`. One possible solution is using brew:
```
# First install brew using:
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
# Then install geos using:
brew install geos
```

## Documentation
`nupylib` uses docstrings for comments. In particular, we use our own system for declaring types. Please take a look at `docs/style_guide.md` for details 

## Testing
`nupylib` uses the standard python unittest framework. To run all tests do:

```
python -m unittest
```

in the root of `nuDeep`. Please make sure all tests run before making a pull request to master.


### Coverage

Neural networks do not easily lend themselves to being tested by simple unittests. Despite this, the goal is that
`nupylib` has "reasonable" unittest coverage. This means that not every single line of code will be
covered. Instead, the goal is to have two different types of unittests:
1. Traditional unittests (see `nupylib/pyt/data/augmentation/tests` for example).
These are small, thorough tests that extensively cover the code. These tests are essential
for functions that are used widely in the code or are extremely difficult to debug.
2. Light unittests (see `nupylib/pyt/nets/tests` for example).
These tests are useful for parts of the code that are too computationally slow to thoroughly test. The goal of these
tests is not to cover all useful scenarios. Instead, these tests are useful as a quick sanity check and serve an important
role when package requirements get upgraded. 

Before merging to master, please include the appropriate unittests.

A decent tool to check unittest code coverage is `coverage.py` which can be installed by `pip install coverage`.

To run, do the following in the root of `nupylib`:
```
bash
coverage erase  # erases old coverage results
coverage run --branch -m unittest discover  # runs all the unittests and gathers the data
coverage html -d <output_directory>  # creates html format of results in <output_directory>
coverage erase  # remove coverage results to make sure you don't accidently commit these to the repo
```
Then point your browser to `<output_directory>/index.html` to view the results.

Note that the overall results of
`coverage` are pretty accurate but it will occassionally miss a few lines, so don't obsess about the coverage results.

## Data

The contents of IMDB and MMDB are [stored on AWS](https://nutonomy.atlassian.net/wiki/spaces/ML/pages/73172132/Compute+setup#Computesetup-AWSs3access).
The image-database is assumed to live in:
```
/data/image-database
```
and the multi modal database in:
```
/data/multimodal-database
```
Experiments are typically stored in:
```
/data/exp/family/name
```
Note, however, that these are just conventions and can be changed if needed. 

### COCO

If you want to use nuDeep to train on the [COCO dataset](http://cocodataset.org), you also need to install the ```pycocotools``` package by doing:
```
$ git clone https://github.com/pdollar/coco.git
$ cd coco/PythonAPI
$ make
$ sudo make install
$ sudo python setup.py install
```
And then add ```coco/PythonAPI``` folder to your ```PYTHONPATH```. 
