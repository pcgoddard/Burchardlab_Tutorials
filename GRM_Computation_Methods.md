# Methods for Computing Genetic Relatedness Matrices (GRMs)
## Pagé Goddard

### Resources

###### TOPmed Pipline 
* [github](https://github.com/UW-GAC/analysis_pipeline)
* [slides](https://uw-gac.github.io/topmed_workshop_2017/computing-a-grm.html)

###### GCTA
* [GCTA Documentation](http://cnsgenomics.com/software/gcta/#GREML)
* [GCTA Publication](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3014363/pdf/main.pdf)

###### REAP
* [REAP Documentation](http://faculty.washington.edu/tathornt/software/REAP/REAP_Documentation.pdf),
* [REAP Publication](http://www.cell.com/ajhg/fulltext/S0002-9297(12)00309-6)

###### GENESIS
* [GENESIS Vignette](https://rdrr.io/bioc/GENESIS/f/vignettes/pcair.Rmd),
* [GENESIS Publication](https://www.ncbi.nlm.nih.gov/pubmed/26748516),
(also see the TOPmed slides above)

### TOPmed Pipeline
The TOPmed analysis pipeline is a great resources for association study design in general, but it is linked here because it includes a recommended approach for GRM computation. For GRM computation with an eye for confounding ancestry, **TOPmed recommends the GENESIS approach**:

1. KING
2. PC-AIR
3. PC-Relate

See below for more details

**NB:** "Section 3 Computing a GRM" calculates a basic Genetic Relationship matrix using `SNPRelate` package in R but does not take into account ancestry or population structure. For the more robust approach, see "Section 4 PC-Relate."

### GCTA
* commandline program
**Importance of GRMs:** Allows for identification of closely related individuals. Objective of downstream `GCTA` analysis is to provide a heritability estimate the genetic variation captured by all SNPs (vs. GWAS which estimates variation captured by single SNPs). Including close relatives could bias the results with variance driven by pedigree phenotypic correlations. 

```bash
# estimate genetic relatedness from SNPs
gcta64 --bfile input.binary --make-grm --out output.files
```

From the publication: *As a by-product, we provide a function in GCTA to **calculate the eigenvectors of the GRM**, which is asymptotically equivalent to those from the PCA implemented in EIGENSTRAT11 because the GRM (Ajk) defined in GCTA is approximately half of the covariance matrix (Jjk) used in EIGENSTRAT. The only purpose of developing this function is to calculate eigenvectors and then include them in the model as covariates to capture variance due to population structure. More sophisticated analyses of the population structure can be found in programs such as EIGENSTRAT and STRUCTURE.*

###### PROs
* super easy to run
    - takes plink files 
    - no extra input required
* fast

######  CONs
* does not take into account ancestry or any external population structure proxy
    - not a reliable estimator for admixed populations
* GCTA has been [criticized](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4987787/pdf/pnas.201608425.pdf) for unreliable estimates (GrantedI haven't read into this too much)
* I (Pagé) don't really understand the math...

### REAP
* commandline program
* concept: use pre-computed ancestry measures to adjust for population structure
* approach: model-based
    - calculate **global ancestry per individual** and **allele frequency per ancestral group** using something like `ADMIXTURE`
    - calculate relatedness coefficients after adjusting for ancestry

###### PROs
* accounts for population ancestry
* designed for admixed populations
* easy one-liner once you have your admixture output

######  CONs
* requires additional inputs calculated externally
    - not tough to calculate; see my [ADMIXTRE Tutorial](https://github.com/pcgoddard/Burchardlab_Tutorials/blob/master/ADMIXTURE_Tut.md)
    - potential for inaccuracies in admixed pop of unknown/poorly defined ancestries
* model-based methods can be confounded by familial relatedness due to inability to distinguish b/w ancestral groups and clusters of close relatives ([source](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3014363/pdf/main.pdf))

### GENESIS
* R
* concept: trianing on unrelated subpopulation and using PCs to correct for population structure
* approach: model-free
    - KING-robust: estimate ancestral divergence and apparent relatedness separately 
    - PC-Air: use PCs to capture the ethnic components of the population structure and identify the unrelated and related clusters
        + ancestral divergence scores used to ensure the unrelated subpop is representative of the full population's ancestry dsitribution
    - PC-Relate: calculate kinship coefficient for unrelated group first, then extrapolate to the related group, using ancestry-representative PCs to correct for pop structure
        + unrelated first: prevent confounding by related individuals
        + using first n PCs: (at your discretion) to capture pop structure / ethnic diversity

###### PROs
* ~~doesn't need external inputs~~
* good track record
    - TOPmed
    - favored in comparison studies (after more computationally heavy IBD-inferrence approaches)
* designed to work well for both admixed and homogenous populations

###### CONs
* PC-relate took about 8 hours to run (R-studio)
* claims to not require external input, but it kind of does - it can all be done in R so you can totally set up a pipeline for it though
    - uses KING-robust estimates (commandline)
    - or SNPRelate funciton (in R)
    - requires some reformatting from either output to prep for PC-Air

---

## Overall winner: GENESIS/TOPmed Pipeline 
see [tutorial](https://github.com/pcgoddard/Burchardlab_Tutorials/blob/master/GENESIS_PCRelate_Tut.md)
