# Creating Singularity images for conda environments: quick guide
Singularity images can be thought of as virtual machines, except only encapsulating everything above the kernel (libraries, user files, etc.). They are also somewhat “transparent”, in that they can “see” the host environment in which they’re run. (This is slightly different from Docker).

**The basic idea** is to create a Singularity image that contains:
* a minimal Linux with conda pre-installed as base “layer”
* all conda packages you want as an additional “layer”

**NOTE** This guide does not cover including third-party software into your image, just packages you can install through conda. Other stuff will be added later.


## Setup
We’ll be creating an image for an environment called `datasci`

### 1. Create a clean conda spec file
**Important Note** The more complex the spec file is, the less likely it is to be successfully built. 

There are 2 ways to obtain simple/clean spec files:
1. create it by hand (easier if the environment has few packages)
2. clean the exported spec file from the existing environment

Here I’ll show **option 2** since it has a few extra steps:

#### 1.1.  Export the spec file `datasci.yaml`
```bash
conda activate datasci
conda env export > datasci.yaml
```

#### 1.2. Clean up the spec file
The best way I find is to delete all packages that you didn’t install yourself, because these are dependencies that will be automatically installed anyway.

The spec file you just exported will initially look like:
```yaml
name: datasci
channels:
  - anaconda
  - conda-forge
  - bioconda
  - defaults
dependencies:
  - blas=1.0=mkl
  - boost-cpp=1.68.0=h11c811c_1000
  - h5py=2.8.0=py36h39dcb92_0
  - hdf5=1.8.18=h525d4c3_0
  - numpy=1.14.3=py36h28100ab_1
  - numpy-base=1.14.3=py36h0ea5e3f_1
  - pandas=0.24.2=py36hf484d3e_0
  - scipy=1.0.0=py36hbf646e7_0
  - pip:
    - alabaster==0.7.10
    - babel==2.5.3
    - coloredlogs==10.0
prefix: /Users/fburic/miniconda3/envs/datasci
```

Delete all packages you don’t remember installing, but keep the first version number. All those version numbers past the second `=` are specific to the OS and python version. It’s best to just remove them. **Careful** that pip expects a double `==`. 

Delete the `prefix` line entirely since it’s specific to the OS it was created from.

In this case, I know I only installed `numpy, scipy, pandas, and coloredlogs` (the last one via `pip`).  

Now the spec file should look like below. Obviously, one could just write it from scratch and update it, as packages are being added.
```yaml
name: datasci
channels:
  - anaconda
  - conda-forge
  - bioconda
  - defaults
dependencies:
  - numpy=1.14.3
  - pandas=0.24.2
  - scipy=1.0.0
  - pip:
    - coloredlogs==10.0
```

**Note** For *tensorflow*, make sure to include `cudatoolkit` and `cudnn`, with the versions appropriate for the *target system* (not you laptop, esp. since macOS only supports a subset of versions). In my case, I have `cudatoolkit=10.1.168` (matching the CUDA version on Vera) and `cudnn=7.6.5`.


### 2. Create a Singularity spec file (“recipe”)

A template exists on Vera at `/apps/Singularity/conda-example.recipe`
This is a slightly adjusted version, that allows installing packages from a spec file. Copy the following in a file called  `datasci.recipe` :

```yaml
Bootstrap: docker
From: continuumio/miniconda3

%files
    datasci.yaml
    
%post
    mkdir -p /cephyr
    mkdir -p /local
    mkdir -p /apps
    mkdir -p /usr/share/lmod/lmod
    mkdir -p /var/hasplm
    mkdir -p /var/opt/thinlinc
    mkdir -p /usr/lib64
    touch /usr/lib64/libdlfaker.so
    touch /usr/lib64/libvglfaker.so
    touch /usr/bin/nvidia-smi

    # Update conda (some versions are buggy: https://github.com/conda/conda/issues/9681)
    /opt/conda/bin/conda update conda
    # Install all packages in the provided spec file 
    /opt/conda/bin/conda env update —name base —file datasci.yaml —prune

%runscript
    exec “$@“
```

**Careful** that you include the spec file with the `%files`  directive. If files aren’t copied into the image, they’re not visible to the building process. Building proceeds in 2 steps: installing the miniconda3 linux+conda, then running all commands in `%post`, including the conda commands.

### 3. Create the singularity image

**Note** You need root privileges.
This might take a while (~20 min), depending on how many packages you have and download times.
Subsequent builds, when only the conda env spec changes, are faster because the base linux image is cached.

```bash
sudo singularity --verbose build datasci.simg datasci.recipe
```

Then all you need is the image file. Just copy it to Vera.

The size of the image is on the order of GBs.  This could be reduced, but still a work in progress on my end.

### 4. Adjust slurm run commands to work with the image

For *tensorflow*, make sure to `module load GCC/8.3.0 iccifort/2019.5.281 CUDA/10.1.168`  (or whatever version works for you)

The way to run things from an image is

```bash
singularity exec --nv datasci.simg COMMAND
```

See more here [Quick Start — Singularity container 3.4 documentation](https://sylabs.io/guides/3.4/user-guide/quick_start.html)

**Notes**
* `—nv` uses NVIDIA drivers
* COMMAND runs stuff **inside** the image, but can still “interact” with the host environment (i.e. can access files on the host OS)
* To run **bash commands**, COMMAND should be `bash -c "command"` 
* Unless configured differently in the Singularity spec file, the `$PATH` and other **environment variables will be different** than the ones on the host OS. Use a bash call to first set up needed env vars, then run your program, e.g. e.g. `bash -c “export PATH=$PATH:/my/path && python my_script.py`

So simply prepend the singularity part to your batch scripts, e.g.:

```bash
#!/bin/bash
#SBATCH -t 0:30:00
#SBATCH -A **project_ID**

singularity exec --nv datasci.simg python my_project/solve_everything.py
```

Note that `my_project/solve_everything.py` are not files in the image, just regularly stored files on the host OS. 

See more here [Using software - C3SE](https://www.c3se.chalmers.se/documentation/software/#singularity)

