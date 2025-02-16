## sc-RNA-seq quality control

🖥️ This pipeline is also available at https://github.com/lijinbio/cellqc

# cellqc: standardized quality control pipeline of single-cell RNA-Seq data

Cellqc standardizes the qualiy control of single-cell RNA-Seq (scRNA) data to render clean feature count matrices from Cell Ranger outputs. Cellqc is implemented using the Snakemake workflow management system to enhance reproduciblity and scalablity of data analysis. Briefly, the QC pipeline starts from raw count feature matrices from Cell Ranger. Dropkick filters out predicted empty droplets, and SoupX purify the transcriptome measurement by substracting the background trancripts. DoubletFinder further detects the potential doublets and retain clean count feature matrices for singlets. Cell types are annotated for clean cells by a reference database using scPred.

![workflow](https://github.com/RCHENLAB/dry-lab-standard/blob/main/workflow.png)

## Installation

Cellqc depends on a number of R and Python packages, so please install the dependencies before installing cellqc. It is encouraged to use Conda to install dependencies as much as possible. E.g.,

```
$ conda create -y -n cellqc python=3.10
$ conda activate cellqc
$ conda config --add channels defaults --add channels bioconda --add channels conda-forge
$ conda install -y mamba
$ conda install -c bioconda cellqc 
$ Rscript -e "remotes::install_github(c('chris-mcginnis-ucsf/DoubletFinder', 'mojaveazure/seurat-disk'))"
$ mamba install -y numpy=1.21 
$ mamba install -y anndata=0.7.8 
$ pip install dropkick
```



To test the installation, simply run

```
cellqc -h
```

## Example to run cellqc 

First, we need to create a folder call tmp with the following command

```
mkdir tmp
```

Go to the *tmp* directory, and create other directory call datataset

```
cd tmp
mkdir dataset
```

Go to dataset directory and create simbolic links to the input data with the *ln* command (for more information about this command, go to *man ln*) 

```
ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/analysis.tar.gz . 
```

This command will create a copy of the archives needed to run cellqc 

For all the .tar.gz files, we need to create a separate folder to each one, as follows: 

```
mkdir analysis 
mkdir  filtered_feature_bc_matrix 
mkdir raw_feature_bc_matrix 
```

For each folder, we need to put in the specific .tar.gz files, decompress the file, and after that, delete the .tar.gz file, as follows: 
```
cd analysis 
ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/analysis.tar.gz . 
tar -xf analysis.tar.gz 
```

Verify if the follow folders are available 1) clustering 2) diffexp 3) pca 3) tsne 4) umap 

After that, don’t forget to delete the analysis.tar.gz file! rm analysis.tar.gz

We need to repeat this process to filtered_feature_bc_matrix.tar.gz and for raw_feature_bc_matrix.tar.gz
```
 cd filtered_feature_bc_matrix.tar
 ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/filtered_feature_bc_matrix.tar.gz .
 tar -xf filtered_feature_bc_matrix.tar.gz 
```

Verify if the follow folders are available: 1) barcodes.tsv.gz 2) features.tsv.gz 3) matrix.mtx.gz 

After that, use rm analysis.tar.gz 

Go to the raw_feature_bc_matrix and repeat the process 

```
 ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/analysis.tar.gz .
 ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/cloupe.cloupe .
 ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/filtered_feature_bc_matrix.h5 .  
ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/filtered_feature_bc_matrix.tar.gz .
 ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/3v3_22_389_retina_N_NeunT/metrics_summary.csv . 
 ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/molecule_info.h5 . 
 ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/possorted_genome_bam.bam . 
 ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/possorted_genome_bam.bam.bai . 
ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/raw_feature_bc_matrix.h5 .  
ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/raw_feature_bc_matrix.tar.gz . 
ln -s /storage/chen/home/u247700/DRG_atlas/jageretal2022/jageretal2022/outs/web_summary.html .
```

If we dont have permission to copy this files, use curl or wget to download in this link:

```
wget https://bcmedu-my.sharepoint.com/:f:/g/personal/u247700_bcm_edu/EqL241kj3pdNkEBdlTOh5iIBnbO5cV2ulYP2FqUEn23yeQ?e=e1gAEP

or just copy from

cp -R /storage/chen/data_share_folder/cellqctutorial/tmp .
```


Next, `cd` to your home directory and make an output folder. `mkdir out`

`cd out` to the output folder and make the required files, config.yaml and sample.txt.

`vim config.yaml` will create an empty file named config.yaml. Copy and paste the following into that file. Copy from this page using CTRL-C; then in vim, press `i` to enter INSERT mode and paste by right-clicking. Hit `esc` to exit INSERT mode and then type `:wq` to save and quit.
```
dropkick:
  skip: true
  method: multiotsu
  numthreads: 1
filterbycount:
  mincount: 500
  minfeature: 300
  mito: 10
doubletfinder:
  skip: false
  findpK: false
  numthreads: 5
  pK: 0.01
scpred:
  skip: true
  reference: /path_to_reference/scPred_trainmodel_RNA_svmRadialWeights_scpred.rds
  threshold: 0.9
```
Example of config file: <https://github.com/RCHENLAB/dry-lab-standard/blob/main/config.yaml>

Then repeat to make the `sample.txt` file. Copy and paste the below. Note that the columns must be tab-separated, and the filepath under 'cellranger' should be pointing to the folder that contains the unpacked symbolic links from earlier (`~/tmp/dataset`). However this filepath MUST be absolute, do not use `~`!

FINALLY let's run cellqc. Make sure you have `cd`ed to the output folder that contains both `sample.txt` and `config.yaml`. Then, with the cellqc conda environment activated, execute the following:

```
slurmtaco.sh -p short -t 5 -m 20G -n mhgcp-d03 -- cellqc -d . -t 8 -c config.yaml -- sample.txt
```  

To verify the queue on taco server, run `squeue -u uYOURID` or (if you have the alias in your bashrc) `sls`.


🔙 [Summary list of pipelines](https://github.com/RCHENLAB/dry-lab-standard/wiki)

