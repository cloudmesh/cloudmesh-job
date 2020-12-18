# Demo of `cms job` in Windows command prompt

# Table of Contents

* [Notation](#Notation)
* [Instalation on Windows](#Instalation-on-Windows)
* [Setting up location of config file](#Setting-up-location-of-config-file)
* [Verification of config file location in `cms job` ](#Verification-of-config-file-location-in-`cms-job`)
* [Creating a template of configuration file](#Creating-a-template-of-configuration-file)
* [Adding a new job in configuration file from another yaml file](#Adding-a-new-job-in-configuration-file-from-another-yaml-file)
* [Adding a new job using command line arguments](#Adding-a-new-job-using-command-line-arguments)
* [Enlisting configured jobs](#Enlisting-configured-jobs)
  * [Enlist all jobs](#Enlist-all-jobs)
  * [Enlist jobs with certain status](#Enlist-jobs-with-certain-status)
  * [Enlist jobs with certain pattern in the job name](#Enlist-jobs-with-certain-pattern-in-the-job-name)
  * [Enlist jobs sorted on job status](#Enlist-jobs-sorted-on-job-status)
* [Submit a job for execution on remote host](#Submit-a-job-for-execution-on-remote-host)
  * [Outputs on remote host](#Outputs-on-remote-host)
  * [Python script used for testing](#Python-script-used-for-testing)
* [Kill a job on remote host](#Kill-a-job-on-remote-host)
  * [Local machine </th>](#Local-machine-</th>)
  * [Remote machine](#Remote-machine)
  * [TODO: SOME OTHER THING THAT WAS NOT IN TABLE](#TODO:-SOME-OTHER-THING-THAT-WAS-NOT-IN-TABLE)
* [Reset status and rerun a job](#Reset-status-and-rerun-a-job)
* [Delete a job from configuration file](#Delete-a-job-from-configuration-file)
* [Remote host management](#Remote-host-management)
* [Enlist hosts](#Enlist-hosts)
* [Job scheduler management](#Job-scheduler-management)
  * [Find out currently configured scheduler](#Find-out-currently-configured-scheduler)
  * [Re-configure the scheduler](#Re-configure-the-scheduler)



## Notation

For better readability some lines in this documentation have been split over
multiple lines. Please adjust whne issuing the commands

TODO: In linux we use \ at the end of the line is there something similar in Windows.

## Instalation on Windows

Create a venv using python 3.8 or python 3.9 in ~/ENV3
Activate the ENV3

TODO: provide how to activate that in windows as different from linux/mac

```
mkdir cm
cd cm
pip install cloudmesh-installer
cloudmesh-installer install jobs
```

## Setting up location of config file

Use `cms job set` to configure location of the configuration file with name 
`jobset`. In this example file `spec.yaml` is used as the configuration file.

```cmd
(ENV3) C:\>cms job set '~/.cloudmesh/job/spec.yaml'
Jobset defined as spec located at ~\.cloudmesh\job\spec.yaml

(ENV3) C:\>cms set jobset
jobset='~\.cloudmesh\job\spec.yaml'
```

## Verification of config file location in `cms job` 

```cmd
(ENV3) C:\>cms job info
Jobs are defined in: ~\.cloudmesh\job\spec.yaml
```

## Creating a template of configuration file

Command `cms job template` can be used to create a sample configuration file:

```
(ENV3) C:\>cms job template --name=job[1-2]
Jobs are defined in: ~\.cloudmesh\job\spec.yaml
```

This generates following content in ~\.cloudmesh\job\spec.yaml. Please note
this command **replaces existing spec.yaml file** with default content.

```yaml
cloudmesh:
  default:
    user: keTan
  jobset:
    hosts:
      localhost:
        name: DESKTOP-HUC37G2
        ip: 127.0.0.1
        cpu_count: 4
        status: free
        job_counter: 0
    scheduler:
      policy: sequential
    jobs:
      job1:
        name: job1
        directory: .
        ip: 127.0.0.1
        input: .
        output: .
        status: ready
        gpu: ''
        user: keTan
        arguments: -lisa
        executable: ls
        shell: bash
      job2:
        name: job2
        directory: .
        ip: 127.0.0.1
        input: .
        output: .
        status: ready
        gpu: ''
        user: keTan
        arguments: -lisa
        executable: ls
        shell: bash
```

## Adding a new job in configuration file from another yaml file

The command `cms job add FILENAME` allows user to add job(s) from a yaml file
to the configuration file. Jobs from the yaml file are appended to the list
of existing jobs.
 
For the demo purpose, a new yaml file called `new.yaml` is created with 
following content:

```yaml
ls_juliet:
  name: ls_juliet
  directory: cm
  ip: juliet.futuresystems.org
  input: ../data
  output: ./output/ls_juliet
  status: ready
  gpu: ' '
  user: ketanp
  arguments: -lisa; bash
  executable: ls
  shell: bash
  host: null
```

Execution of `cms job add FILENAME` adds this job to the configured list of 
jobs:

Currently configured jobs:

```cmd
(ENV3) C:\>cms job list
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml
         file '~\.cloudmesh\job\spec.yaml'
+--------+------------+-----------+-----------+---------+-----------+-------+
| Number | JobName    | JobStatus | RemoteIp  | Command | Arguments | User  |
+--------+------------+-----------+-----------+---------+-----------+-------+
| 1      | job1       | ready     | 127.0.0.1 | ls      | -lisa     | keTan |
| 2      | job2       | ready     | 127.0.0.1 | ls      | -lisa     | keTan |
| 3      | pytest_job | ready     | local     | ls      | -lisa     | user  |
+--------+------------+-----------+-----------+---------+-----------+-------+
```
Adding the job from `new.yaml` and checking list of jobs:
```cmd
(ENV3) C:\>cms job add '~/.cloudmesh/job/new.yaml'
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml
         file '~\.cloudmesh\job\spec.yaml'

(ENV3) C:\>cms job list
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml
         file '~\.cloudmesh\job\spec.yaml'
+--------+------------+-----------+--------------------------+---------+-------------+--------+
| Number | JobName    | JobStatus | RemoteIp                 | Command | Arguments   | User   |
+--------+------------+-----------+--------------------------+---------+-------------+--------+
| 1      | job1       | ready     | 127.0.0.1                | ls      | -lisa       | keTan  |
| 2      | job2       | ready     | 127.0.0.1                | ls      | -lisa       | keTan  |
| 3      | pytest_job | ready     | local                    | ls      | -lisa       | user   |
| 4      | ls_juliet  | ready     | juliet.futuresystems.org | ls      | -lisa; bash | ketanp |
+--------+------------+-----------+--------------------------+---------+-------------+--------+ 
```

## Adding a new job using command line arguments

Command `cms job add` also allows users to add a new job in the list of 
configured jobs from command line:

```cmd
(ENV3) C:\>cms job add --name='sample'
                       --ip=localhost
                       --executable='python sample.py'
                       --arguments='--gpu=7'
                       --directory='./scripts'
                       --input='./data'
                       --output='./output'
                       --status='ready'
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml
         file '~\.cloudmesh\job\spec.yaml'

(ENV3) C:\>cms job list
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml file '~\.cloudmesh\job\spec.yaml'
+--------+------------+-----------+--------------------------+------------------+-------------+--------+
| Number | JobName    | JobStatus | RemoteIp                 | Command          | Arguments   | User   |
+--------+------------+-----------+--------------------------+------------------+-------------+--------+
| 1      | job1       | ready     | 127.0.0.1                | ls               | -lisa       | keTan  |
| 2      | job2       | ready     | 127.0.0.1                | ls               | -lisa       | keTan  |
| 3      | pytest_job | ready     | local                    | ls               | -lisa       | user   |
| 4      | ls_juliet  | ready     | juliet.futuresystems.org | ls               | -lisa; bash | ketanp |
| 5      | sample     | ready     | localhost                | python sample.py | --gpu=7     | keTan  |
+--------+------------+-----------+--------------------------+------------------+-------------+--------+
```

## Enlisting configured jobs

Command `cms job list` allows users to enlist all the jobs configured in the 
configuration file. This job also shows some basic details of these jobs such
as job name, job status, executable, remote host IP and the user.
There are few variation of this command as follows:

### Enlist all jobs

```cmd
(ENV3) C:\>cms job list
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml file '~\.cloudmesh\job\spec.yaml'
+--------+-------------+-----------+--------------------------+------------------+-------------+--------+
| Number | JobName     | JobStatus | RemoteIp                 | Command          | Arguments   | User   |
+--------+-------------+-----------+--------------------------+------------------+-------------+--------+
| 1      | job1        | ready     | 127.0.0.1                | ls               | -lisa       | keTan  |
| 2      | job2        | ready     | 127.0.0.1                | ls               | -lisa       | keTan  |
| 3      | pytest_job  | ready     | local                    | ls               | -lisa       | user   |
| 4      | ls_juliet   | submitted | juliet.futuresystems.org | ls               | -lisa; bash | ketanp |
| 5      | sample      | killed    | localhost                | python sample.py | --gpu=7     | keTan  |
| 6      | pytest_job1 | ready     | localhost                | python sample.py | --gpu=7     | keTan  |
+--------+-------------+-----------+--------------------------+------------------+-------------+--------+
```
### Enlist jobs with certain status

```cmd
(ENV3) C:\>cms job list --status='submitted'
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml file '~\.cloudmesh\job\spec.yaml'
+--------+-----------+-----------+--------------------------+---------+-------------+--------+
| Number | JobName   | JobStatus | RemoteIp                 | Command | Arguments   | User   |
+--------+-----------+-----------+--------------------------+---------+-------------+--------+
| 1      | ls_juliet | submitted | juliet.futuresystems.org | ls      | -lisa; bash | ketanp |
+--------+-----------+-----------+--------------------------+---------+-------------+--------+
```

### Enlist jobs with certain pattern in the job name

```cmd
(ENV3) C:\>cms job list --name='pytest'
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml file '~\.cloudmesh\job\spec.yaml'
+--------+-------------+-----------+-----------+------------------+-----------+-------+
| Number | JobName     | JobStatus | RemoteIp  | Command          | Arguments | User  |
+--------+-------------+-----------+-----------+------------------+-----------+-------+
| 1      | pytest_job  | ready     | local     | ls               | -lisa     | user  |
| 2      | pytest_job1 | ready     | localhost | python sample.py | --gpu=7   | keTan |
+--------+-------------+-----------+-----------+------------------+-----------+-------+
```

### Enlist jobs sorted on job status

```cmd
(ENV3) C:\>cms job status
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml file '~\.cloudmesh\job\spec.yaml'
+--------+-------------+-----------+--------------------------+------------------+-------------+--------+
| Number | JobName     | JobStatus | RemoteIp                 | Command          | Arguments   | User   |
+--------+-------------+-----------+--------------------------+------------------+-------------+--------+
| 5      | sample      | killed    | localhost                | python sample.py | --gpu=7     | keTan  |
| 1      | job1        | ready     | 127.0.0.1                | ls               | -lisa       | keTan  |
| 2      | job2        | ready     | 127.0.0.1                | ls               | -lisa       | keTan  |
| 3      | pytest_job  | ready     | local                    | ls               | -lisa       | user   |
| 6      | pytest_job1 | ready     | localhost                | python sample.py | --gpu=7     | keTan  |
| 4      | ls_juliet   | submitted | juliet.futuresystems.org | ls               | -lisa; bash | ketanp |
+--------+-------------+-----------+--------------------------+------------------+-------------+--------+
```  

## Submit a job for execution on remote host

```cmd
(ENV3) C:\>cms job run --name=test_juliet
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml
         file '~\.cloudmesh\job\spec.yaml'
```

### Outputs on remote host

```cmd
[ketanp@j-login1 test_juliet]$ pwd
/N/u/ketanp/output/test_juliet

[ketanp@j-login1 test_juliet]$ cat test.log
2020-08-02 03:45:04,662|test.py|DEBUG|Preparing debug logs.
2020-08-02 03:45:04,662|test.py|DEBUG|Arguments passed:
2020-08-02 03:45:04,662|test.py|DEBUG|{'--gpu': '7',
 '--input': './data',
 '--output': './output/test_juliet'}
2020-08-02 03:45:04,662|test.py|DEBUG|Starting the py script.
2020-08-02 03:45:04,662|test.py|DEBUG|Processing sleep 20
2020-08-02 03:45:24,682|test.py|DEBUG|complete
2020-08-02 03:45:24,683|test.py|DEBUG|Processing another sleep 20
2020-08-02 03:45:44,690|test.py|DEBUG|complete
2020-08-02 03:45:44,690|test.py|DEBUG|End of the script

[ketanp@j-login1 test_juliet]$ cat test_juliet_pid.log
11092

[ketanp@j-login1 test_juliet]$

```

### Python script used for testing

```python
from __future__ import print_function
import time, sys
from docopt import docopt
import logging

usage="""
Usage:
    test.py --output=OUTPUT --input=INPUT --gpu=GPU

Options:
    --output=OUTPUT    Location of script output
    --input=INPUT      Location of inputs
    --gpu=GPU          GPU to be used for script execution
"""
arguments = docopt(usage)

out_dir = arguments.get('--output')
in_dir = arguments.get('--input')
gpu = arguments.get('--gpu')

logging.basicConfig(filename='{}/test.log'.format(out_dir),
                    level=logging.DEBUG,
                    format='%(asctime)s|%(filename)s|%(levelname)s|%(message)s')

logging.debug("Preparing debug logs.")


logging.debug("Arguments passed: ")
logging.debug(arguments)

logging.debug("Starting the py script.")

logging.debug("Processing sleep 20")

time.sleep(20)
logging.debug("complete")

logging.debug("Processing another sleep 20")
time.sleep(20)
logging.debug("complete")

logging.debug("End of the script")
```

## Kill a job on remote host

### Local machine </th>

```cmd
(ENV3) C:\>cms job run --name=test_juliet
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml file
'~\.cloudmesh\job\spec.yaml'


(ENV3) C:\>cms job kill --name=test_juliet
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml file
'~\.cloudmesh\job\spec.yaml'
```

### Remote machine

```bash
[ketanp@j-login1 test_juliet]$ while [ 1 == 1 ];
> do
> ps -ef | grep 'test.py'
> echo "======================================="
> sleep 3
> done
ketanp   18170  3720  0 04:43 pts/8    00:00:00 grep --color=auto test.py
=======================================
ketanp   18173  3720  0 04:43 pts/8    00:00:00 grep --color=auto test.py
=======================================
ketanp   18182  3720  0 04:43 pts/8    00:00:00 grep --color=auto test.py
=======================================
ketanp   18185 18183  0 04:43 ?        00:00:00 bash -c cd ./;sh -c 'echo $$ > ./output/test_juliet/test_juliet_pid.log;
exec python test.py --input=./data --output=./output/test_juliet --gpu=7'
ketanp   18214 18185  0 04:43 ?        00:00:00 python test.py --input=./data --output=./output/test_juliet --gpu=7
ketanp   18216  3720  0 04:43 pts/8    00:00:00 grep --color=auto test.py
=======================================
ketanp   18185 18183  0 04:43 ?        00:00:00 bash -c cd ./;sh -c 'echo $$ > ./output/test_juliet/test_juliet_pid.log;
exec python test.py --input=./data --output=./output/test_juliet --gpu=7'
ketanp   18214 18185  0 04:43 ?        00:00:00 python test.py --input=./data --output=./output/test_juliet --gpu=7
ketanp   18219  3720  0 04:43 pts/8    00:00:00 grep --color=auto test.py
=======================================
ketanp   18185 18183  0 04:43 ?        00:00:00 bash -c cd ./;sh -c 'echo $$ > ./output/test_juliet/test_juliet_pid.log;
exec python test.py --input=./data --output=./output/test_juliet --gpu=7'
ketanp   18214 18185  0 04:43 ?        00:00:00 python test.py --input=./data --output=./output/test_juliet --gpu=7
ketanp   18231  3720  0 04:43 pts/8    00:00:00 grep --color=auto test.py
=======================================
ketanp   18185 18183  0 04:43 ?        00:00:00 bash -c cd ./;sh -c 'echo $$ > ./output/test_juliet/test_juliet_pid.log;
exec python test.py --input=./data --output=./output/test_juliet --gpu=7'
ketanp   18214 18185  0 04:43 ?        00:00:00 python test.py --input=./data --output=./output/test_juliet --gpu=7
ketanp   18237  3720  0 04:43 pts/8    00:00:00 grep --color=auto test.py
=======================================
ketanp   18276  3720  0 04:43 pts/8    00:00:00 grep --color=auto test.py
=======================================
ketanp   18279  3720  0 04:43 pts/8    00:00:00 grep --color=auto test.py
=======================================
```

### TODO: SOME OTHER THING THAT WAS NOT IN TABLE

```cmd
(ENV3) C:\>cms job list --name='juliet'
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml
         file '~\.cloudmesh\job\spec.yaml'
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
| Number | JobName     | JobStatus | RemoteIp                 | Command        | Arguments                                        | User   |
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
| 1      | test_juliet | killed    | juliet.futuresystems.org | python test.py | --input=./data --output=./output/test_juliet     | ketanp |
|        |             |           |                          |                | --gpu=7                                          |        |
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
```

## Reset status and rerun a job

```cmd
(ENV3) C:\>cms job list --name='juliet'
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml
         file '~\.cloudmesh\job\spec.yaml'
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
| Number | JobName     | JobStatus | RemoteIp                 | Command        | Arguments                                        | User   |
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
| 1      | test_juliet | killed    | juliet.futuresystems.org | python test.py | --input=./data --output=./output/test_juliet     | ketanp |
|        |             |           |                          |                | --gpu=7                                          |        |
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+


(ENV3) C:\>cms job reset --name='test_juliet'
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml
         file '~\.cloudmesh\job\spec.yaml'
Status reset for job test_juliet.


(ENV3) C:\>cms job list --name='juliet'
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml file '~\.cloudmesh\job\spec.yaml'
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
| Number | JobName     | JobStatus | RemoteIp                 | Command        | Arguments                                        | User   |
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
| 1      | test_juliet | ready     | juliet.futuresystems.org | python test.py | --input=./data --output=./output/test_juliet     | ketanp |
|        |             |           |                          |                | --gpu=7                                          |        |
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+


(ENV3) C:\>cms job run --name=test_juliet
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml
         file '~\.cloudmesh\job\spec.yaml'


(ENV3) C:\>cms job list --name='juliet'
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml file '~\.cloudmesh\job\spec.yaml'
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
| Number | JobName     | JobStatus | RemoteIp                 | Command        | Arguments                                        | User   |
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
| 1      | test_juliet | submitted | juliet.futuresystems.org | python test.py | --input=./data --output=./output/test_juliet     | ketanp |
|        |             |           |                          |                | --gpu=7                                          |        |
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
``` 

## Delete a job from configuration file

Command `cms job delete` provides an ability to users to delete a job from 
the configuration file. The delete operation first kills a job if it is in 
`submitted` status and then deletes it from the job set.

- Checking existing list of jobs:
```cmd
(ENV3) C:\>cms job list
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml
         file '~\.cloudmesh\job\spec.yaml'
+--------+-------------+-----------+--------------------------+------------------+--------------------------------------------------+--------+
| Number | JobName     | JobStatus | RemoteIp                 | Command          | Arguments                                        | User   |
+--------+-------------+-----------+--------------------------+------------------+--------------------------------------------------+--------+
| 1      | job1        | ready     | 127.0.0.1                | ls               | -lisa                                            | keTan  |
| 2      | job2        | ready     | 127.0.0.1                | ls               | -lisa                                            | keTan  |
| 3      | pytest_job  | ready     | local                    | ls               | -lisa                                            | user   |
| 4      | test_juliet | submitted | juliet.futuresystems.org | python test.py   | --input=./data --output=./output/test_juliet     | ketanp |
|        |             |           |                          |                  | --gpu=7                                          |        |
| 5      | sample      | killed    | localhost                | python sample.py | --gpu=7                                          | keTan  |
| 6      | pytest_job1 | submitted | juliet.futuresystems.org | ls               | -lisa                                            | ketanp |
+--------+-------------+-----------+--------------------------+------------------+--------------------------------------------------+--------+
```
- Deleting `sample` job and checking the list again

```cmd
(ENV3) C:\>cms job delete --name=sample
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml
         file '~\.cloudmesh\job\spec.yaml'


(ENV3) C:\>cms job list
WARNING: The key 'cloudmesh.profile.user' could not be found in the yaml file '~\.cloudmesh\job\spec.yaml'
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
| Number | JobName     | JobStatus | RemoteIp                 | Command        | Arguments                                        | User   |
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
| 1      | job1        | ready     | 127.0.0.1                | ls             | -lisa                                            | keTan  |
| 2      | job2        | ready     | 127.0.0.1                | ls             | -lisa                                            | keTan  |
| 3      | pytest_job  | ready     | local                    | ls             | -lisa                                            | user   |
| 4      | test_juliet | submitted | juliet.futuresystems.org | python test.py | --input=./data --output=./output/test_juliet     | ketanp |
|        |             |           |                          |                | --gpu=7                                          |        |
| 5      | pytest_job1 | submitted | juliet.futuresystems.org | ls             | -lisa                                            | ketanp |
+--------+-------------+-----------+--------------------------+----------------+--------------------------------------------------+--------+
```


## Remote host management

Command `cms job hosts` facilitates configuration of remote hosts in the 
configuration file. 


```cmd
(ENV3) C:\>cms job hosts add --hostname='juliet'
                             --ip='juliet.futuresystems.org'
                             --cpu_count='12'
job hosts add --hostname='juliet' --ip='juliet.futuresystems.org' --cpu_count='12'
WARNING: The key 'cloudmesh.profile.user' could not be found in the
         yaml file '~\.cloudmesh\job\spec.yaml'
```
```yaml
  hosts:
    localhost:
      name: DESKTOP-HUC37G2
      ip: 127.0.0.1
      cpu_count: 4
      status: free
      job_counter: '0'
    juliet:
      name: juliet
      ip: juliet.futuresystems.org
      cpu_count: '12'
      status: free
      job_counter: '0'
```

## Enlist hosts

```bash
(ENV3) C:\>cms job list hosts
job list hosts
+-----------------+--------------------------+-----------+--------+-------------+
| name            | ip                       | cpu_count | status | job_counter |
+-----------------+--------------------------+-----------+--------+-------------+
| DESKTOP-HUC37G2 | 127.0.0.1                | 4         | free   | 0           |
| juliet          | juliet.futuresystems.org | 12        | free   | 0           |
+-----------------+--------------------------+-----------+--------+-------------+
```

## Job scheduler management

The command `cms job scheduler` enables users to configure job scheduler 
policies. These policies come in effect when the host configured with the job
is not available for further job submissions. In such scenario, `cms job 
run` searches the next available host based on the scheduler policy and 
submits the job on that host.
It is assumed that this next host has all input data needed for the job and 
also the output locations. Available scheduler policies are:

* sequential - Use first available host
* random     - Use random available host
* smart      - Use a host with highest availability
* frugal     - Use a host with least availability
  
### Find out currently configured scheduler

```cmd 
(ENV3) C:\>cms job scheduler info

INFO: Configured scheduler policy: sequential
```

### Re-configure the scheduler

```cmd
(ENV3) C:\>cms job scheduler --policy=smart

INFO: Scheduler policy changed from sequential to smart
```