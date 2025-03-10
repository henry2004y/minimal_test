---
title: Profiling
subtitle: With Intel OneAPI
tags:
  - computer
categories:
  - Blog
---

The Intel profiler VTune and Trace Analyzer are now bundled in the OneAPI toolkit. It works best for C/C++ codes, and decent for Fortran. I have some good time using the GUI interface for submitting parallel jobs, but sometimes it may be faster to utilize the command line interface.

## Installation

For large systems like Frontera, it is as easy as

```bash
module load oneapi
module load vtune
```

## Collecting VTune Data

```bash
ibrun -n 4 vtune -c hotspots -r hotspots_base -- ./heart_demo.base -m ../mesh_mid -s ../setup_mid.txt -t 10 -i
ibrun -n 4 vtune -c hpc-performance -r hpcperf_base -- ./heart_demo.base -m ../mesh_mid -s ../setup_mid.txt -t 10 -i
ibrun -n 4 vtune -c threading -r threading_base -- ./heart_demo.base -m ../mesh_mid -s ../setup_mid.txt -t 10 -i
```

Note that in able to trigger backtracing to source codes, typically `-g` or debugging mode is required. Stack tracing should be enabled.

## Analyzing VTune Data

VTune has a nice GUI that is typically named `vtune-gui`. We can open the generated profiling reports inside the GUI and check all kinds of metrics.

## Trace Analyzer

This is Intel's tool for improving MPI efficiency.

### Collecting Data

If we use Intel's MPI library,

```sh
mpirun -n 16 -trace program.exe
```

For other MPI libraries, the corresponding dynamic library must be loaded. Note that OpenMPI is not compatible with Trace Analyzer.

### Analyzing Tracer Data

Trace Analyzer also has a nice GUI.

```sh
traceanalyzer
```
