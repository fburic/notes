# SLURM

## Interplay with Conda and Snakemake

Loading `Anaconda3` and activating the environment needs to be **last** and preferably **only once**. Otherwise, the environment stops seeing packages, cannot be deactivated.

### hebbe  (slurm 17.11.5)

* works: preload modules + conda env (in `.bashrc`) and have snakefiles or batch scripts assume all things available (don't load anything in them)

### kebnekaise (slurm 17.11.9-2)

(not completely sure)

* modules and environment need to be loaded in the job script / snakefile
* preloading in .bashrc doesn't work and seems to mess up conda env  (conda packages not visible) 
* works: snakemake `shell.prefix()`
* doesn't work:  snakemake `conda` option  (conda packages not visible)


## Check job usage

```
sacct -o JobID,RSS,ReqMem,MaxVMSize,MaxRSS,MaxRSSTask,State,NodeList -j <JobID>
sacct -j <jobid> -o jobid,jobname,state,exitcode,derivedexitcode
```


## Watch queue status

```bash
check_job_status(){
    while :
    do
        echo "[ $(date "+%H:%M:%S") ]"
        squeue -u $USER
        # Go up 4 lines to overwrite
        echo -e "\e[4A"
        sleep 5
    done
}
```


## Install Anaconda3 Lmod module for SLURM with EasyBuild

**Assumption**: There is an EasyBuild config file for Anaconda3.
One can search with `eb -S '^Anaconda3'`

Start a fresh session (either with `ml purge` or new bash session),
then run the instructions below from your home directory.

**NOTE!** Replace `projects` with your own project directory on the
high-capacity storage partition. Here I'm assuming `projects` is a symlink in
the home directory but you could write the absolute path to the storage partition.

```bash
# EasyBuild will download files and build the module in ~/.local
# This process involves O(1e4) files so best use a symlink to
# a high-capacity storage area
mkdir projects/.local
ln -s projects/.local .local

# The .conda also gets largs so make sure to put it somewhere else
ln -s projects/.conda .conda

module load EasyBuild/3.7.1
eb Anaconda3-5.1.0.eb
module use .local/easybuild/modules/all/

module load Anaconda3

# Add personal module at login
echo "module use .local/easybuild/modules/all/" >> ~/.bashrc
```


## Lmod configuration (incl creating aliases for modules)

https://lmod.readthedocs.io/en/latest/093_modulerc.html
