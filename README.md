# Parallel Gibbs Sampler

This project contains the implementations of the parallel Gibbs
samplers described in:

> Parallel Gibbs Sampling: From Colored Fields to Think Junction Trees by Joseph Gonzalez, Yucheng Low, Arthur Gretton, and Carlos Guestrin

Because the main `sampler.cpp` binary currently only supports the Alchemy
intermediate factor format we recommend using the precompiled `matlab`
binaries in the `matlab` folder.  The matlab interface uses a very
simple discrete factorized model representation that is easy to construct.

The two main binaries `sampler` and `make_denoise_alchemy` execute the sample and create a sample Alchemy input file:

1. **`sampler.cpp`**: This is the main point of entry for the Gibbs sampler binary.  To learn more about how to use this run `./sampler --help`.  The sampler binary operates on alchemy factor-graph files.

2. **`make_denoise_alchemy.cpp`**: This file is used to create a simple
    synthetic image denoising problem.  To learn more about this file
    run `./make_denoise_alchemy`.

## Alchemy Factor Graph Format:

The Alchemy factor graph format was developed in collaboration with the
Alchemy team (http://alchemy.cs.washington.edu/) at the University of
Washington.  The format describes a discrete factorized model as a
text file with the following form:

    variables:
    <varid>\t<nstates>
    ...
    factors:
    <varid> / <varid> / ... / <varid> // <logP(0, 0, ..., 0)> <logP(1, 0, ..., 0)> ...

The remaining files are described below:

* `factorized_model.hpp/cpp`: This file is used to read and parse Alchemy files into GraphLab data structures to represent the factorized model.  Note this file also contains the key definitions of many of the various types (vertex_id_t, variable_t, ...) used in the Gibbs sampler. 

* `mrf.hpp/cpp`: This file defines the key data-structures needed to assemble a GraphLab graph representing a Markov Random Field. In addition this file defines routines to construct an MRF from a factorized model. 

* `global_variable.hpp/cpp`: This file defines the few global variables which are used to access the factors from within the threads (without needing to make copies) as well as the GraphLab GLShared objects which are GraphLab managed global variables (formerly part of the Shared Data Table).

* `chromatic_sampler.hpp/cpp`: This file defines the key parts of the chromatic sampler algorithm including the update function as well as a helper routine used to launch the chromatic sampler for a series of timed experiments.

* `junction_tree.hpp/cpp`: This file defines helper routine to construct junction_trees from a subset of the variables in the MRF.  In particular this file contains the extend() routine which is described in the original paper.  Finally, this file contains routines to build a GraphLab graph representation of a junction tree to run parallel calibration and sampling on the junction tree.

* `jt_splash_sampler.hpp/cpp`: This rather long file contains the bulk of the junction tree Splash sampler.  The key parts of which are the jt_splash_sampler object and its dependent jt_worker object. The jt_splash_sampler maintains ncpus jt_workers.  Each jt_worker has access to the shared MRF and asynchronously constructs Splashes. In addition the splash_settings object describes the settings for the jt_splash_sampler.

* `pgibbs_tls.hpp/cpp`: This file defines thread local storage objects used to reduce memory allocations when running the various sampling algorithms.  


