# Property Prediction
This repository contains graph convolutional networks (or message passing network) for molecule property prediction. 

## Installation
Requirements:
 * cuda >= 8.0 + cuDNN
 * Python 3/conda: Please follow the installation guide on [https://conda.io/miniconda.html](https://conda.io/miniconda.html)
   * Create a conda environment with `conda create -n <name> python=3.7`
   * Activate the environment with `conda activate <name>`
 * pytorch: Please follow the installation guide on [https://pytorch.org/](https://pytorch.org/)
 * RDKit: `conda install -c rdkit rdkit`
 * Other packages: `pip install -r requirements.txt`

## Training
To train a model, run:
```
python train.py --data_path <path> --dataset_type <type> --save_dir <dir>
```
where `<path>` is the path to a CSV file containing a dataset, `<type>` is either "classification" or "regression" depending on the type of the dataset, and `<dir>` is the directory where model checkpoints will be saved.

For example:
```
python train.py --data_path data/tox21.csv --dataset_type classification --save_dir tox21_checkpoints

```
By default, the script will train the network for 30 epochs and save the best model in `$DIR/model.best`.
The data file must be be a **CSV file with a header row**. For instance:
```
smiles,NR-AR,NR-AR-LBD,NR-AhR,NR-Aromatase,NR-ER,NR-ER-LBD,NR-PPAR-gamma,SR-ARE,SR-ATAD5,SR-HSE,SR-MMP,SR-p53
CCOc1ccc2nc(S(N)(=O)=O)sc2c1,0,0,1,,,0,0,1,0,0,0,0
CCN1C(=O)NC(c2ccccc2)C1=O,0,0,0,0,0,0,0,,0,,0,0
...
```

Note:
* Classification is assumed to be binary.
* Empty values in the CSV are ignored.
* `--save_dir` may be left out if you don't want to save model checkpoints.

## Deepchem test
We tested our model on 14 deepchem benchmark datasets (http://moleculenet.ai/), ranging from physical chemistry to biophysics
properties. To run our model on those datasets,  
```
bash run.sh 1
```
where 1 is the random seed for randomly splitting the dataset into training, validation and testing (not applied to datasets with scaffold splitting).

### Results

We compared our model against the graph convolution in deepchem. Our results are averaged over 3 runs with different random seeds, namely different splits accross datasets.

Results on classification datasets (AUC score, the higher the better)

| Dataset   |	Ours   |	GraphConv (deepchem)   |
| :-------------: |:-------------:| :-----:|
| Bace	| 0.825 ± 0.011	| 0.783 ± 0.014 |
| BBBP	| 0.692 ± 0.015	| 0.690 ± 0.009 |
| Tox21	| 0.849 ± 0.006	| 0.829 ± 0.006 |
| Toxcast	| 0.726 ± 0.014	| 0.716 ± 0.014 |
| Sider |	0.638 ± 0.020	| 0.638 ± 0.012 |
| clintox	| 0.919 ± 0.048	| 0.807 ± 0.047 |
| MUV	| 0.067 ± 0.03 | 0.046 ± 0.031 |
| HIV |	0.763 ± 0.001 |	0.763 ± 0.016 |
| PCBA	| 0.218 ± 0.001 | 	0.136 ± 0.003 | 

Results on regression datasets (score, the lower the better)

Dataset	| Ours	| GraphConv/MPNN (deepchem) |
| :-------------: |:-------------:| :-----:|
delaney	| 0.66 ± 0.07 | 	0.58 ± 0.03 |
Freesolv |	1.06 ± 0.19	| 1.15 ± 0.12 |
Lipo |	0.642 ± 0.065 |	0.655 ± 0.036 |
qm8 |	0.0116 ± 0.001 | 0.0143 ± 0.0011 |
qm9 |	2.6 ± 0.1	| 3.2 ± 1.5 |
