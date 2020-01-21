# GARBO
In clinical applications, feature selection is a crucial step for the selection of low dimensional biomarkers from high 
dimensional data generated by Omics technologies. Here we introduce GARBO, a novel multi-island adaptive genetic algorithm to 
simultaneously optimize accuracy and set size in omics-driven biomarker discovery problems. 

The GARBO algorithm is currentely implemented in python.

## Installation

Install phython 2.7 and install  the following python modules within your virtual environment
sklearn, multiprocessing, matplotlib, operator, bisect, deap, numpy, random, skfuzzy, cPickle, pandas, scipy, sys, getopt
source activate 'your_virtual_env'

## Examples

To run GARBO in parallel on a computer desktop

```sh
export NGEN=100 # Indicate the number of GA-iterationsexport 
NPOP=50 # Indicate the number of the chromosomes to be generated for each nicheexport 
MINL=30 # Indicate the initial minimum length of the generated chromosomesexport 
MAXL=50 # Indicate the initial maixmum length of the generated chromosomesexport 
NN=2 # Specify the number of nichesexport 
RN=1 # Indicate if an initial rank of the features must be compiled (RN=1) otherwise it starts with no ranking information (RN=0).
export INPUT_FILE="data_ccle_erl_ge.csv" # Omics dataset (samples as columns and rows as features< The last feature must named 'class' and it correpsonds to the target label)
export OUTPUT_DIR="MRNA_run_1"    # Folder that will contain a file for each nicheserialized python-obejcts. Each file contains the
mkdir $OUTPUT_DIR
nohup python runGARBO.py -g $NGEN -p $NPOP -s $MINL -l $MAXL -n $NN -r $RN -i $INPUT_FILE -o $OUTPUT_DIR > output_mrna.log &
```

To run GARBO in parallel on a multi-node server.

First we create a bash script to launch one GARBO-task (e.g. 'garbo_one_task.sh').

```sh
#!/bin/bash
#SBATCH --mail-type=END
#SBATCH --mail-user=##################

source activate garbo
python runGARBO.py -g $NGEN -p $NPOP -s $MINL -l $MAXL -n $NN -r $RN -i $INPUT_FILE -o $OUTPUT_DIR
conda deactivate
```

Then, we create a second bash script indicting the setting for a run in parallel. 

```sh
#!/bin/bash
#SBATCH --partition parallel
#SBATCH --time 2-00:00:00       # Runtime in minutes.
#SBATCH --mem-per-cpu=5000
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=10
#SBATCH --array=1-10
export NGEN=500
export NPOP=500
export MINL=30
export MAXL=50
export NN=10
export RN=1
export INPUT_FILE="train_ge_$SLURM_ARRAY_TASK_ID.csv"
export OUTPUT_DIR="GE_view_$SLURM_ARRAY_TASK_ID"
mkdir $OUTPUT_DIR
srun -o ccle_erl_ge$SLURM_ARRAY_TASK_ID.out -e ccle_ge_$SLURM_ARRAY_TASK_ID.err garbo_one_task.sh
```

### Contact Information
Vittorio Fortino <vittorio.fortino@uef.fi>
Dario Greco <dario.greco@utune.fi>
