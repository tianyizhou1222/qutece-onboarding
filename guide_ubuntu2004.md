# Medical Image Processing Tools on Ubuntu 20.04 LTS
author: Tianyi Zhou

---------

# Python version management

## System python on Ubuntu
It seems Ubuntu 20.04 comes with python 3.8 by default, which should be the system python version. (No python2 was found)

## pip and pip3
First, install pip for python 3 using apt:

```
sudo apt install python3-venv python3-pip
```
I'm still not clear how pip and pip3 works. Just check if `pip` points to python3. Try running `pip --version`, `pip3 --version` to check.

## conda
Conda is a package manager developed by Anaconda. After installation, you will have a default environment named base, which shows up as `(base)username@computername` when you open a terminal. For the most of the time, the python version in your conda base environment and that in your system are differnt. In my case, my system python is version 3.6.9 while in conda base environment it's version 3.7.4. Also, some neuroimaging analysis tool are not available through conda. Since we don't mess up with different python versions, we avoid using conda as much as possible... But we do need it for vmtk installation.

To install Ananconda, follow this instruction: [https://docs.anaconda.com/anaconda/install/linux/](https://docs.anaconda.com/anaconda/install/linux/)

Anaconda automatically activates base environment for every newly opend terminal. To turn off this option, run

```
conda config --set auto_activate_base false
```

# Install python packages
## dcm2niix

```
sudo apt-get install dcm2niix
```

## Imaging related packages: dcm2bids, nibabel, nipype, itk,
1. dcm2bids
2. itk
3. nibabel
4. nilearn
5. nipype
6. scikit-image
7. scikit-learn
8. gdcm

```
pip install dcm2bids itk nibabel nipype nilearn python-gdcm scikit-image scikit-learn
```
## Genearl packages used in scientific research
1. numpy
2. pandas
3. scipy
4. matplotlib
5. seaborn
```
pip install numpy pandas scipy matplotlib seaborn
```

# NeuroDebian
NeuroDebian provides some popular neuroscience softwares for easy installation via apt on Debian OS. For Ubuntu 18.04 and a US-NH server, use this command to add NeuroDebian repo to native package mangement system:

```
wget -O- http://neuro.debian.net/lists/bionic.us-nh.full | sudo tee /etc/apt/sources.list.d/neurodebian.sources.list
sudo apt-key adv --recv-keys --keyserver hkp://pool.sks-keyservers.net:80 0xA5D32F012649A5A9
```

Then update the package index:

```
sudo apt-get update
```

For other OS and different servers, go to [this page](http://neuro.debian.net/)

## afni(NeuroDebian)

```
sudo apt-get install afni
```

This will automatically install afni into the default directory /usr/bin, not sure how to change that yet.
And this installs version Debian-18.0.05 while latest version is 20.0.03. might need manual installation as well...

## Other packages requiring manual downloading
### afni
A bunch steps needed for installation. [Here is a detailed step-by-step instruction for Ubuntu 18.04](https://afni.nimh.nih.gov/pub/dist/doc/htmldoc/background_install/install_instructs/steps_linux_ubuntu18.html)


### fsl
+ Download installer from [its website](https://fsl.fmrib.ox.ac.uk/fsldownloads_registration)
+ Run installer using python2. `-d` option specifies the directory you want to install into. (This installer will download fsl and then install so it might take some time...)

```
cd ~/Downloads
python2 fslinstaller.py -d /opt/fsl
```
+ Shell setup: add the following scripts into `~/.bashrc`
```
FSLDIR=/opt/fsl
. ${FSLDIR}/etc/fslconf/fsl.sh
PATH=${FSLDIR}/bin:${PATH}
export FSLDIR PATH
```
Then don't forget to update the environment by running `source .bashrc` in terminal.


### bart
+ Dependencies

```
sudo apt-get install make gcc libfftw3-dev liblapacke-dev libpng-dev libopenblas-dev
```

+ Download the package and unzip to installation folder `/opt`

```
cd ~/Downloads
wget https://github.com/mrirecon/bart/archive/v0.5.00.tar.gz
tar -C /opt -xzvf v0.5.00.tar.gz
```

+ Compile

```
cd /opt/bart-0.5.00
make
```
### freesurfer
+ Downlaod the package and install into `/opt`

```
cd ~/Downloads
wget https://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/6.0.0/freesurfer-Linux-centos6_x86_64-stable-pub-v6.0.0.tar.gz
tar -C /opt -xzvf freesurfer-Linux-centos6_x86_64-stable-pub-v6.0.0.tar.gz
```

+ Setup the environment variable (if your shell is bash). Just add two lines to your .bashrc file(normally in your home directory)

```
export FREESURFER_HOME=/opt/freesurfer
source $FREESURFER_HOME/SetUpFreeSurfer.sh
```

Then update the environment by running `source .bashrc` in terminal.

### ANTs
[Official instruction on compiling ANTs on Linux](https://github.com/ANTsX/ANTs/wiki/Compiling-ANTs-on-Linux-and-Mac-OS)

Use `git clone` instead of `wget` to download the source code to avoid [version number issue](https://github.com/ANTsX/ANTs/issues/1183)

+ Make directory and git clone the source code to `/opt/ants-git`

```
cd /opt
mkdir ants-git
cd ants-git
git clone https://github.com/ANTsX/ANTs.git
```

+ Build configuration

```
mkdir build
cd build
cmake \
    -DCMAKE_INSTALL_PREFIX=/opt/ANTs \
    ../ANTs 2>&1 | tee cmake.log
```

+ Build step

Using 12 threads to save time:

```
make -j 12 2>&1 | tee build.log
```

+ Install step

```
cd ANTS-build
make install 2>&1 | tee install.log
```

### MATLAB

+ Go to its website, log in and download the installer
+ In the unzipped folder, run `./install`
+ Change the installation directory to `/opt` and follow the instructions until finished
+ Finally, setup the environment variable so you can call it from terminal easily. Add the following line to your .bashrc file

```
PATH="/opt/MATLAB/R2019b/bin:${PATH}"
```
+ Update the environment by running `source .bashrc` in terminal.

### spm12
+ Download [spm12.zip](https://www.fil.ion.ucl.ac.uk/spm/software/download/) and its updates [spm12_updates_rxxxx.zip](https://www.fil.ion.ucl.ac.uk/spm/download/spm12_updates/) in `~/Downloads` then type the following in a Terminal, this will install spm to directory `/opt`:

```
cd ~/Downloads
unzip spm12.zip -d /opt
unzip -o spm12_updates_rxxxx.zip -d /opt/spm12
```

+ Start MATLAB and add SPM into your path, either using File > Set Path > Add Folder... or typing the following command in MATLAB's workspace.

```
addpath /opt/spm12
```

+ In a Terminal, from the src folder of your SPM12 installation, type:

```
cd /opt/spm12/src
make distclean
make && make install
make external-distclean
make external && make external-install
```

#### Unring function dependencies
+ This is an optional step (but required in Liam's customized nipype workflow). This is to setup the matlab dependencies for the unring function.
+ 3 major dependencies are required:
	- unring tool implemented in MATLAB [see codes here](https://github.com/josephdviviano/unring/tree/master/matlab)
	- Unring_Nii.m (codes written by LT)
	- get_3D_Unring.m (codes written by LT)
+ Put these dependencies into one folder (e.g. `~/matlab`), and add this folder to matlab path


### vmtk (using conda - this works)

+ Install Ananconda [https://docs.anaconda.com/anaconda/install/linux/](https://docs.anaconda.com/anaconda/install/linux/)

+ Install and update anaconda-client

```
conda install anaconda-client
conda update conda anaconda-client
conda config --set restore_free_channel true
```

+ Create a new python environment (version=3.6.1) named vmtk using conda. We are being very specific about the version number (3.6.1 instead of 3.6) because vmtk seems to have issue with python3.6.13, which is the current (Mar24,2021) defualt version under python3.6 installed using conda.

```
conda create -n vmtk python=3.6.1
```

+ Activate the environment you just created

```
source activate vmtk
```

+ Install vtk from ananconda channel

```
conda install -c anaconda vtk=8.1.0
```

+ Install itk and vmtk from vmtk channel

```
conda install -c vmtk itk vmtk
```

+ Notes on installation: vtk on vmtk channel crashes and gives openGL error during rendering. So the workaround is: install vtk from anaconda channel instead, then install itk and vmtk from vmtk channel. [Check the group discussion here.](https://groups.google.com/forum/#!msg/vmtk-users/IbVEv2p64Tc/XWNDt88RAwAJ)

+ To use this vmtk environment within a Jupyter Notebook
```
conda activate vmtk
conda install -c anaconda ipykernel
ipython kernel install --user --name=vmtk
```

+ To remove this environment
```
jupyter kernelspec uninstall vmtk
```

### vmtk (build-from-source - this doesn't work for now)
+ Prerequisites: git, python, cmake, gcc.
+ For Ubuntu, we also need the following packages. Run

```
sudo apt-get install libxt-dev libgl1-mesa-glx libgl1-mesa-dev libglapi-mesa libosmesa-dev build-essential
```

+ Download the source code using git:

```
cd ~/Downloads
mkdir vmtk-source
cd vmtk-source
git clone https://github.com/vmtk/vmtk.git
```

+ Create a build directory under `/opt` and run cmake with the vmtk-source directory:

```
cd /opt
mkdir vmtk
cd vmtk
cmake ~/Downloads/vmtk-source/vmtk
make
```

+ Then it will build itk and vtk, give an error when building vmtk. For now(01-29-2020), there is no obvious workaround for this compiling failure... Check [this page](https://github.com/vmtk/vmtk/issues/341#issuecomment-577912907) for updates on the error.


# Other tools

## Syncthing

### Troubleshooting: Recover data from other nodes to a replaced hard drive
* Create empty folders on new hard drive, with the same path previously set in Syncthing
* Start syncthing
* Set the folder type on the local node to "receive-only"
* Set the folder type on all remote nodes to "send-only", to avoid accidental data lost
* Quit syncthing and run the following command in Terminal

```
syncthing -reset-database
```

* Start syncing
