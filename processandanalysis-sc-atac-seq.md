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


So rn there's an error in the Signac package (basically a conflict with Seurat versioning; it's specifically in the `TSSEnrichment(fast = FALSE)`, an error with a slot not being recognized. This has been [reported](https://github.com/stuart-lab/signac/issues/1538) as an issue with Signac that can be solved by to the `develop` branch of signac on Github. However this package isn't on Anaconda, so to install it to our environment open R and run the code listed:
* `remotes::install_github("stuart-lab/signac", ref="develop")`
* Create a yaml of your environment's current dependencies. With the environment activated, run `conda env export > env_scatac.yml`
* `conda env update --name myenv --file local.yml --prune`


🔙 [Summary list of pipelines](https://github.com/RCHENLAB/dry-lab-standard/wiki)
