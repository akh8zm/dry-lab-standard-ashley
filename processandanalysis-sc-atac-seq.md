## Processing and analyzing single-cell ATAC-seq data

✔️ This pipeline is also available at [Github](https://github.com/GreenleafLab/ArchR)


🖥️ [A Brief Tutorial of ArchR](https://www.archrproject.com/articles/Articles/tutorial.html)

We're currently using signac for this, tutorial available [here](https://stuartlab.org/signac/articles/pbmc_vignette.html#integrating-with-scrna-seq-data)

Create a folder for your analysis, then `wget` the four files as instructed. Change the permissions of all four files by running `chmod 777 path/to/folder/*`.

Then create a conda environment and use mamba to install needed packages.
```
mamba install -y r-signac bioconductor-ensdb.hsapiens.v75 r-seurat
# some other things you'll need that (for some reason) were not automatically installed as dependencies:
# r-hdf5r
# bioconductor-biovizbase
```
DO NOT INSTALL PACKAGES FROM WITHIN R; always install using conda first, then just `library` them once you're in R.




🔙 [Summary list of pipelines](https://github.com/RCHENLAB/dry-lab-standard/wiki)
