# List of tasks completed for Independent Study 

* Semester: May 2020 
* Course number: SU20:  DSCI-D699

branch indicated in brackets

Submitted by: Ketan Pimparkar

1. Documentation

   1. (dev) [Documented the use of single/multiple GPUs in a python script.](https://github.com/cloudmesh/cloudmesh-timeseries/blob/dev/README-MultiGPU.md)
   
      1. Gregor added Nvidia toolkit download instructions for ubuntu 20.04

   2. (hopkins) [Worked on gathering references for timeseries/Covid data.](https://github.com/cloudmesh/cloudmesh-timeseries/blob/hopkins/timeseries.bib)
    
      1. Gregor fixed format

1. cloudmesh-timeseries  

   1. (hopkins) [Built a Covid-19 dataset similar to IU dataset.](https://github.com/cloudmesh/cloudmesh-timeseries/blob/hopkins/notes.md)
   
   2. (hopkins) [Integrated Hopkins Covid-19 dataset.](https://github.com/cloudmesh/cloudmesh-timeseries/commit/0b05586567422739b888305c8924b0a8c13b6687)
   
   3. (hopkins) [Created visualization of Covid-19 geographic data.](https://github.com/cloudmesh/cloudmesh-timeseries/blob/hopkins/notebook/Geo_plot_hopkins.ipynb)
   
   4. (hopkins) [Tested existing timeseries visualizations on Hopkins dataset.](https://github.com/cloudmesh/cloudmesh-timeseries/blob/hopkins/notebook/Hopkins.ipynb)
   
   5. (master) [Worked on Date.py to add method "between" and added corresponding pytest.](https://github.com/cloudmesh/cloudmesh-timeseries/commit/38cb59f41179288d185562f867f096afe085feb0)
        
   7. (master) [Benchmarked lstm-predict-n.py on romeo and colab.](https://github.com/cloudmesh/cloudmesh-timeseries/tree/master/notebook/benchmarks)
   
   8. (dev) [Included GRU algorithm for Covid-19 timeseries analysis.](https://github.com/cloudmesh/cloudmesh-timeseries/commit/e94cad5171bb3fafa595e00e2151a528a2cb9703)
   
    
2. cloudmesh-job

   1. (main) [./loudmesh/cloudmesh-job Worked on the development of cloudmesh-job module.](https://github.com/cloudmesh/cloudmesh-job)
   
   2. (main) [./cloudmesh/job/command/job.py Worked on methods such as job run/kill/delete.](https://github.com/cloudmesh/cloudmesh-job/blob/main/cloudmesh/job/command/job.py)
   
   3. (main) [README-windows.md Documented demo of the cloudmesh job workflow.](https://github.com/cloudmesh/cloudmesh-job/blob/main/README-windows.md)
   
   4. (main) [./tests Created pytests and documented results.](https://github.com/cloudmesh/cloudmesh-job/tree/main/tests)
   
   5. (main) [pytest to validate execution of a job on GPU is created.](https://github.com/cloudmesh/cloudmesh-job/blob/main/tests/test_03_job_gpu.py)
   
   6. (main) [Parameter 'max_jobs_allowed' is added at host configuration level](https://github.com/cloudmesh/cloudmesh-job/commit/2f9c2507e35bed0136d3807b51436a82370850b6)
