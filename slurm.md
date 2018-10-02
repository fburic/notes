## Check job usage

```
sacct -o JobID,RSS,ReqMem,MaxVMSize,MaxRSS,MaxRSSTask,State,NodeList -j <JobID>
sacct -j <jobid> -o jobid,jobname,state,exitcode,derivedexitcode
```
