# Distributed Dask on Gadi
This repository provides comprehensive instructions on configuring a distributed Dask Cluster on the Gadi supercomputer using the PBS job scheduler.

To use interface woth the PBS job scheduler on Gadi we need the following python library.

```

from dask_jobqueue import PBSCluster

```

We will be using a Python virtual environment for this tutorial. The virtual environment is available in __/scratch/vp91/Training-Venv/dask/dask-venv__. All the requirements of that virtual environment are available in the text file __requirements.txt__. 

We can can use the following command to deploy a Dask cluster using Dask.

```
cluster = PBSCluster(walltime="00:50:00", 
                     cores=48, 
                     memory="192GB",
                     shebang='#!/usr/bin/env bash',
                     job_extra_directives=extra, 
                     local_directory='$TMPDIR', 
                     job_directives_skip=["select"], 
                     interface="ib0",
                     job_script_prologue=setup_commands,
                     python=os.environ["DASK_PYTHON"])

```

Where 
1. **walltime**: Maximum walltime for each worker job.
2. **cores**: Total number of cores per job.
3. **shebang**: Path to desired interpreter for your batch submission script.
4. **job_extra_directives**: List of other PBS options. Each option will be prepended with the #PBS prefix.
5. **local_directory**: Dask worker local directory for file spilling.
6. **job_directives_skip**: Directives to skip in the generated job script header. Directives lines containing the specified strings will be removed. Directives added by job_extra_directives won’t be affected.
7. **interface**: Network interface like ‘eth0’ or ‘ib0’. This will be used both for the Dask scheduler and the Dask workers interface
8. **job_script_prologue**: Commands to add to script before launching worker
9. **python**: Python executable used to launch Dask workers. Defaults to the Python that is submitting these jobs

Gadi uses a custom PBS so some of the default values to the fucntion may not work on Gadi. For instance the default PBS expects __#PBS -A <project name>__ to specify the project we are using, but the PBS in Gadi use __#PBS -P <project name>__. All these extra directves is mentioned in __job_extra_directives__. Similarly, there may be some default directives we may need to skip. This is __job_directives_skip__.

Most HPC clusters comes with latency reducing networks like Infiiniband. We can instruct dask to use a specific  using __interface__. 

We also need to load modules and activate our virual environemnt for each node in the cluster. These instructions are mentioned using __job_script_prologue__. 
