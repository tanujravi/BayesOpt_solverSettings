# Optimizing OpenFOAM's GAMG settings via Bayesian optimization

This project aims to use Bayesian optimization for finding the optimal solver settings in OpenFOAM. 
The basis of the project can be found in the repository 
[Learning of optimized solver settings for CFD applications](https://github.com/JanisGeise/learning_of_optimized_multigrid_solver_settings_for_CFD_applications)


## Getting started - local execution

### Dependencies

The instructions and tests are tailored to:
- OpenFOAM-v2406
- Python 3.10

Newer versions might work as well but were not explicitly tested.

To set up a suitable virtual environment, run:
```bash
# repository top-level
python3 -m venv bopt
source bopt/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

### Running an optimization

```
source bopt/bin/activate
python3 run.py example_config_local.yaml &> log.example_run
```

The script *eval_runs.py* contains a rudimentary example for visualizing the outcome.

## Getting started - cluster execution

### Allocating a workspace

The instructions in this subsection are specific to TU Dresden's *Barnard* system.
The following command creates a workspace named *general_testing* that is valid for 90 days.
```bash
ws_allocate -F horse -r 7 -m firstname.lastname@tu-dresden.de -n general_testing -d 90
cd /data/horse/ws/$USER-general_testing
```
For more details on the workspace allocation, refer to the [quick start guide](https://compendium.hpc.tu-dresden.de/quickstart/getting_started/).

### Dependencies

First, clone the repository to your workspace:
```bash
git clone https://github.com/JanisGeise/BayesOpt_solverSettings
cd BayesOpt_solverSettings
```

To set up a suitable virtual environment, run:
```bash
# repository top-level
module load release/24.04 GCCcore/12.2.0 Python/3.10.8
python -m venv bopt
source bopt/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

For more details on the HPC system, refer to the documentation:
- [Python virtual environments](https://compendium.hpc.tu-dresden.de/software/python_virtual_environments/)
- [OpenFOAM on Barnard](https://compendium.hpc.tu-dresden.de/software/cfd/#openfoam)

### Running an optimization

The driver script has to be started via a *jobscirpt*. A suitable jobscript looks as follows:
```
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --tasks-per-node=1
#SBATCH --time=08:00:00
#SBATCH --mail-type=start,end
#SBATCH --mail-user=<your.email>@tu-dresden.de

module load release/24.04 GCCcore/12.2.0 Python/3.10.8
source bopt/bin/activate
python run.py example_config_slurm.yaml &> log.example_run
```
To submit the job, run:
```
sbatch jobscript
```

## Test case

The current test case is a 2D, laminar flow past a cylinder taken from the
[flow_data](https://github.com/AndreWeiner/flow_data) repository.

More test cases etc. will follow


## Still TODO

- early stopping
- test different optimization configs for ax