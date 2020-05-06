# SUPERGNOVA

SUPERGNOVA (SUPER GeNetic cOVariance Analyzer) is a statistical framework to perform local genetic covariance analysis. SUPERGNOVA only needs GWAS summary data and a reference panel as input data. The preprint is available at biorxiv.

## Requirements

The software is developed and tested in Linux and Mac OS environments. The following softwares and packages are required:

1. **Python 2.7**
2. **numpy**
3. **scipy**
4. **pandas**
5. **sklearn**
6. **bitarray**

## Tutorial

You can download SUPERGNOVA by:

```
$ git clone https://github.com/qlu-lab/SUPERGNOVA
$ cd ./SUPERGNOVA
```

Suppose you would like to calculate local genetic covariance between  Crohn's disease and ulcerative colitis. We'll need a few types of files:

- **Summary statistics files:** You can get your own GWAS summary statistics files for these two diseases [here](https://www.ibdgenetics.org). We assume that the files are in the standard format that ``ldsc`` understands. If not, make sure to run them through the included ``munge_sumstats.py`` file or use the one included in ``ldsc`` (see [here](https://github.com/bulik/ldsc/wiki/Heritability-and-Genetic-Correlation#reformatting-summary-statistics) for instructions).

- **Plink bfiles:** These are files .bed/.bim/.fam format. You can download an example that we have prepared for you by:
```
$ mkdir ./data
$ mkdir ./data/bfiles
$ wget ftp://ftp.biostat.wisc.edu/pub/lu_group/Projects/SUPERGNOVA/bfiles/* -P ./data/bfiles/
``` 
These files are from the 1000 Genomes Project, with rare variants (MAF < 5\%) filtered out.

- **Genome partition files**: These files should be in bed format. Please note that different population may have different genome partition. An example dataset for European population can be downloaded by:
```
$ mkdir ./data/partition
$ wget ftp://ftp.biostat.wisc.edu/pub/lu_group/Projects/SUPERGNOVA/partition/* -P ./data/partition/
```

More details about these supplied files can be found in here.

You may run the following command:

```
python2 supergnova.py ./data/CD.sumstats.gz ./data/UC.sumstats.gz \
--N1 27726 \
--N2 28738 \
--bfile data/bfiles/eur_chr@_SNPmaf5 \
--partition data/partition/eur_chr@.bed \
--out results.txt
```
### Explanation of Command-Line Arguments

- The first two arguments, `./data/CD.sumstats.gz` and `./data/UC.sumstats.gz`, denote the locations of the first and second summary statistics files. These files may be compressed using gzip, bz2, zip, xz, or not compressed at all. The program will infer the compression method if the files end with .gz, .bz2, .zip, xz, respectively. As previously mentioned, we assume that the files are in the standard format that `ldsc` understands.

- The `N1` and `N2` arguments (optional) denote the sample sizes of the summary statistics files. If they are not provided, they will be inferred from the summary statistics files.

- The `bfile` argument denotes the prefix of the `.bed/.bim/.fam` genotypic data files. Note the '@', which denotes a wildcard character that SUPERGNOVA will be replaced with 1-22. Alternatively, if you have one set of genotypic data files with 22 chromosome combined, you can just specify one bfile. We recommend you use the whole genome data as the reference panel although you may only be interested in the local genetic correlation in one specific region. 

- The `partition` argument denotes the genome partition file in bed format. Note the '@', which denotes a wildcard character that SUPERGNOVA will be replaced with 1-22. Alternatively, if you are only interested in some specific genomic regions, you can just specify one bed file that summarizes all the regions that you want to look into.

- The `out` flag denotes the file location for the results to be outputted to.

### Additional Command-Line Arguments

- `--thread`: The thread number used for calculation (optional). If they are not provided, the default is the number of detected CPUs.

### Explanation of Output
The output will be a whitespace-delimited text file, with the rows corresponding to different annotations and the columns as such:

- `chr`: The chromosome.
- `start`: The start position of the genomic region from the input genome partition file.
- `end`: The end position of the genomic region from the input genome partiition file.
- `rho`: The estimation of local genetic covariance.
- `corr`: The estimation of local genetic correlation.
- `h1`: The estimation of local heritability of the first trait by method of moments.
- `h2`: The estimation of local heritability of the second trait by method of moments.
- `var`: The variance of the estimation of local genetic covariance.
- `p`: The p value of local genetic covariance.
- `m`: The number of SNPs involved in the estimation of local genetic covariance in the genomic region.

NOTE: The true heritability of some genomic regions for some traits may be very small. Although methods for estimating local heritability exist, they may provide unstable, in many cases negative heritability estimates. SUPERGNOVA ignores negative heritability estimates, leaving the correlation estimates as 'NA'. So, we recommend the users to focus on genetic covariance instead of genetic correlation when performing local genetic covariance analysis.


## Credits

Those using the SUPERGNOVA software should cite: Zhang, Y.L. et al. Local genetic correlation analysis reveals heterogeneous etiologic sharing of complex traits. 2020.

The LD score calculation  and the estimation of phenotypic covariance are adapted from `ldsc.py` in  `ldsc` and `ldsc_thin.py` in `GNOVA`. See [Bulik-Sullivan, B. et al. An Atlas of Genetic Correlations across Human Diseases and Traits. Nature Genetics, 2015.](https://www.nature.com/articles/ng.3406) and [Lu, Q.S. et al. A powerful approach to estimating annotation-stratified genetic covariance using GWAS summary statistics. The American Journal of Human Genetics, 2017.](https://www.cell.com/ajhg/fulltext/S0002-9297(17)30453-6)
