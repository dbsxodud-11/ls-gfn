# LS-GFN
Official Code for Local Search GFlowNets

*Note: I find that there are some differences in terms of mode metrics and hyperparameters. Now it is fixed.*

### Environment Setup
Please first install your conda with yaml file and install the pyg (of pytorch 1.13). 
(https://pytorch-geometric.readthedocs.io/en/latest/install/installation.html)
```
conda env create -f environment.yaml

pip install torch_geometric
pip install pyg_lib torch_scatter torch_sparse torch_cluster torch_spline_conv -f https://data.pyg.org/whl/torch-1.13.0+cu117.html

conda activate ls_gfn
```

### Code references
Our implementation is based on "Towards Understanding and Improving GFlowNet Training" (https://github.com/maxwshen/gflownet). 

### Our contribution (in terms of codes)

We extend codebase with RNA-binding tasks designed by FLEXS (https://github.com/samsinai/FLEXS) 

We implement detailed balance (DB), sub-trajectory balance (SubTB), and our method LS-GFN on top of DB, SubTB, TB, MaxEnt and GTB. 

We also implement various state-of-the-art baselines including RL approaches (A2C-Entropy, SQL, PPO) and recent MCMC approaches (MARS)

### Large files

To run `sehstr` task, you should download `sehstr_gbtr_allpreds.pkl.gz` and `block_18_stop6.pkl.gz`. Both are available for download at https://figshare.com/articles/dataset/sEH_dataset_for_GFlowNet_/22806671
DOI: 10.6084/m9.figshare.22806671
These files should be placed in `datasets/sehstr/`.


### Main Experiments
We should run all experiments with at least 3 different random seeds.
You can see example script in `scripts/<task_name>.sh`

For QM9 task, you can run experiments by following commands
```
# TB + LS-GFN
python runexpwb.py --setting qm9str --beta 5 --model tb --ls true --deterministic true --num_active_learning_rounds 2000 --seed 0

# TB
python runexpwb.py --setting qm9str --beta 5 --model tb --num_active_learning_rounds 2000 --seed 0

# MARS
python runexpwb.py --setting qm9str --beta 5 --model mars --num_active_learning_rounds 2000 --seed 0

# A2C
python runexpwb.py --setting qm9str --beta 5 --model a2c --num_active_learning_rounds 2000 --seed 0

# SQL
python runexpwb.py --setting qm9str --beta 5 --model sql --num_active_learning_rounds 2000 --seed 0

# PPO
python runexpwb.py --setting qm9str --beta 5 --model ppo --num_active_learning_rounds 2000 --seed 0
```

### Evaluation
After training, you can run the following commands to evaluate the performance of models. 

`eval.py` returns top-100 reward, diversity, unique fraction of samples generated by trained models

`number_of_modes.py` returns the total number of modes discovered over the course of training.
```
python eval.py --setting qm9str --beta 5 --model tb --ls true --deterministic true --seed 0
python number_of_modes.py --setting qm9str --beta 5 --model tb --ls true --deterministic true --seed 0
```

### Different GFN Objectives
If you want to run LS-GFN with different training objectives, you can run following commands:

- MaxEnt
    ```
    # MaxEnt + LS-GFN
    python runexpwb.py --setting qm9str --beta 5 --model maxent --ls true --deterministic true --num_active_learning_rounds 2000 --seed 0

    # MaxEnt
    python runexpwb.py --setting qm9str --beta 5 --model maxent --num_active_learning_rounds 2000 --seed 0
    ```

- DB
    ```
    # DB + LS-GFN
    python runexpwb.py --setting qm9str --beta 5 --model db --ls true --deterministic true --num_active_learning_rounds 2000 --seed 0

    # DB
    python runexpwb.py --setting qm9str --beta 5 --model db --num_active_learning_rounds 2000 --seed 0
    ```

- SubTB
    ```
    # SubTB + LS-GFN
    python runexpwb.py --setting qm9str --beta 5 --model subtb --lamda 0.9 --ls true --deterministic true --num_active_learning_rounds 2000 --seed 0

    # SubTB
    python runexpwb.py --setting qm9str --beta 5 --model subtb --lamda 0.9 --num_active_learning_rounds 2000 --seed 0
    ```

- GTB
    ```
    # GTB + LS-GFN
    python runexpwb.py --setting qm9str --beta 5 --model gtb --ls true --deterministic true --num_active_learning_rounds 2000 --seed 0

    # GTB
    python runexpwb.py --setting qm9str --beta 5 --model gtb --num_active_learning_rounds 2000 --seed 0
    ```

### Ablations

- Filtering Strategies (Deterministic vs Stochastic)
    ```
    # Deterministic
    python runexpwb.py --setting qm9str --model tb --ls true --deterministic true --num_active_learning_rounds 2000 --seed 0

    # Stochastic
    python runexpwb.py --setting qm9str --model tb --ls true --num_active_learning_rounds 2000 --seed 0
    ```

- Number of Deconstruction / Reconstruction Steps (K)
    ```
    python runexpwb.py --setting qm9str --model tb --ls true --deterministic true --k <reconstruction_steps> --num_active_learning_rounds 2000 --seed 0
    ```

- Number of Revision Steps (I)
    ```
    python runexpwb.py --setting qm9str --model tb --ls true --deterministic true --i <revision_steps> --num_active_learning_rounds 2000 --seed 0
    ```

- Different Mode Metrics 
    ```
    # QM9 - Diversity
    python number_of_modes.py --setting qm9str --model tb --ls true --deterministic true --mode_metric div_threshold_05 --seed 0
    python number_of_modes.py --setting qm9str --model tb --ls true --deterministic true --mode_metric div_threshold_075 --seed 0

    # RNA - Distance
    python number_of_modes.py --setting rna --rna_task 1 --rna_length 14 --model tb --ls true --deterministic true --mode_metric hamming_ball1 --seed 0
    python number_of_modes.py --setting rna --rna_task 1 --rna_length 14 --model tb --ls true --deterministic true --mode_metric hamming_ball2 --seed 0
    ```
