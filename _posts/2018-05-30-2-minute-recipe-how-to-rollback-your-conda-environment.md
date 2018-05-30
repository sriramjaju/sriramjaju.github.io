---
layout: post
published: true
title: '2 minute recipe: How to rollback your conda environment'
---
I was working on a personal Machine learning project for which I was using [Anaconda](https://anaconda.org/ "Anaconda") environment with python 2.7(yes I know) because of some dependencies.

I was trying to upgrade the sklearn library and I accidentally updated Python to 3.6. 
I was about to delete my whole environment and recreate it again from YAML file(thank god I had that), but then I referred conda documentation and there it was, the magic command to save me from all this trouble.


```bash
List the history of each change to the current environment     conda list --revisions
Restore environment to a previous revision                       conda install --revision 2
```

The best way to explain is by a quick example. If you run conda list --revisions, you’ll get an output like this:


```bash
2017-04-18 23:29:36  (rev 1)
     requests  {2.12.3 -> 2.13.0}

2018-05-30 19:41:47  (rev 2)
     mkl  {11.3.3 -> 2018.0.2}
     numpy  {1.11.2 -> 1.14.3}
     pip  {9.0.1 -> 10.0.1}
     python  {2.7.12 -> 3.6.5}
     scikit-learn  {0.17.1 -> 0.19.1}
     scipy  {0.18.1 -> 1.1.0}
     setuptools  {27.2.0 -> 39.1.0}
     wheel  {0.29.0 -> 0.31.1}
    +blas-1.0
    +certifi-2018.4.16
    +icc_rt-2017.0.4
    +intel-openmp-2018.0.0
    +mkl_fft-1.0.1
    +mkl_random-1.0.1
    +numpy-base-1.14.3
    +vc-14
    +vs2015_runtime-14.0.25123
    +wincertstore-0.2
```

As you can see in the output that it list each revision along with updated packages(old version -> new version) and newly added packages(the one with + symbol).
So now you know what changes were make in each revision, you can safely rollback to the previous versions of your environment by using
conda install --revision _revision number_.

In my case I reverted to rev 1, so after rollback when I run conda list --revisions again, I can see the changes done in rollback.


```bash
2018-05-30 20:08:46  (rev 3)
     mkl  {2018.0.2 -> 11.3.3}
     numpy  {1.14.3 -> 1.11.2}
     pip  {10.0.1 -> 9.0.1}
     python  {3.6.5 -> 2.7.12}
     scikit-learn  {0.19.1 -> 0.17.1}
     scipy  {1.1.0 -> 0.18.1}
     setuptools  {39.1.0 -> 27.2.0}
     wheel  {0.31.1 -> 0.29.0}
    -blas-1.0
    -certifi-2018.4.16
    -icc_rt-2017.0.4
    -intel-openmp-2018.0.0
    -mkl_fft-1.0.1
    -mkl_random-1.0.1
    -numpy-base-1.14.3
    -vc-14
    -vs2015_runtime-14.0.25123
    -wincertstore-0.2
```

You can see that the changes for revision 3 are just the inverse of revision 2.

This comes handy if you accidentally screw up your environment.

More info:

Conda User Guide: https://conda.io/docs/user-guide/tasks/manage-environments.html

Conda Cheat Sheet: https://conda.io/docs/_downloads/conda-cheatsheet.pdf

If you have any suggestions let me know on twitter: [@Sriramjaju](https://twitter.com/Sriramjaju "@Sriramjaju")