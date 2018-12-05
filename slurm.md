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
