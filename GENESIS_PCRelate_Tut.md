# GENESIS PC-Relate Tutorial
#### Pagé Goddard

"`GENESIS` uses `PC-AiR` for **population structure** inference that is robust to known or cryptic relatedness, and it uses `PC-Relate` for accurate **relatedness estimation** in the presence of population structure, admixutre, and departures from Hardy-Weinberg equilibrium."

### Resources

[GENESIS Vignette](https://rdrr.io/bioc/GENESIS/f/vignettes/pcair.Rmd), 
[Bioconductor Vignette](https://www.bioconductor.org/packages/devel/bioc/vignettes/GENESIS/inst/doc/pcair.html#plink-files)

[KING Documentation](http://people.virginia.edu/~wc9c/KING/manual.html)

[SNPRelate](https://www.rdocumentation.org/packages/SNPRelate/versions/1.6.4)

### Libraries
```R
# be sure to install biocLite
source("https://bioconductor.org/biocLite.R")
biocLite(c('GENESIS',"GWASTools", "SNPRelate"))
library(GENESIS)
library(GWASTools)
library(SNPRelate)
library(gdsfmt)
```

### Input Files
GENESIS uses a model-free approach and thus requires only the genotype file and no externally calculated ancestry proportions. The functions in the `GENESIS` package read genotype data from a GenotypeData class object created by the `GWASTools` package. Through the use of `GWASTools`, a `GenotypeData` class object can easily be created from:

* Plink Files
* GDS File
* R Matrix of Genotype Data

#### PLINK files
The `SNPRelate` package provides the `snpgdsBED2GDS` function to convert binary PLINK files into a GDS file.

file       | description
-----------|-----------
`bed.fn`   | path to `PLINK.bed` file
`bim.fn`   | path to `PLINK.bim` file
`fam.fn`   | path to `PLINK.fam` file
`out.gdsfn`|path for output GDS file

```R
snpgdsBED2GDS(bed.fn = "genotype.bed", bim.fn = "genotype.bim", fam.fn = "genotype.fam", out.gdsfn = "mygenotype.gds")
```

Then continue with GDS file instructions:

#### GDS files

```R
mygeno <- GdsGenotypeReader(filename = "PATH_TO/mygenotype.gds")
myenoData <- GenotypeData(geno)
```

#### R Matrix
file       | description
-----------|-----------
`genotype`   | matrix of genotype values coded as 0 / 1 / 2; rows index SNPs; columns index samples
`snpID`      | integer vector of unique SNP IDs
`chromosome` | integer vector specifying chr of each snp
`position`   | integer vector psecifying position of each SNP
`scanID`     | vector of unique individual IDs

```R
mygeno <- MatrixGenotypeReader(genotype = genotype, snpID = snpID, chromosome = chromosome, position = position, scanID = scanID)

mygenoData <- GenotypeData(mygeno)
```

### Pairwise Measures of Ancestry Divergence
Identifying a **mutually unrelated** and **ancestry representative** subset of individuals. `KING-robust` kinship coefficient estimator provides negative estimates for unrelated pairs with divergent ancestry to prioritize ancestrally-diverse individuals for the representative subset. Can be calculated with `KING-robust` from `GENESIS` or `snpgdsIBDKING` from `SNPRelate`.

##### with KING-robust
###### KING (external [program](http://people.virginia.edu/~wc9c/KING/manual.html))
* **input**: PLINK binary ped bile  `genotype.bed`
* **command**: king -b `genotype.bed` --kinship
* **output**: `king.kin`, `king.kin0`

KING Output: `.kin` and `.kin0` text files. `king2mat` extracts kinship coefficients for `GENESIS` functions. 

```bash
king -b genotype.bed --kinship
```

```r
# sample output

head king.kin # within family
##  FID     ID1     ID2     N_SNP   Z0      Phi     HetHet  IBS0    Kinship Error
##  28      1       2       2359853 0.000   0.2500  0.162   0.0008  0.2459  0
##  28      1       3       2351257 0.000   0.2500  0.161   0.0008  0.2466  0
##  28      2       3       2368538 1.000   0.0000  0.120   0.0634  -0.0108 0
##  117     1       2       2354279 0.000   0.2500  0.163   0.0006  0.2477  0
##  117     1       3       2358957 0.000   0.2500  0.164   0.0006  0.2490  0

head king.kin0 # between family
##  FID1    ID1     FID2    ID2     N_SNP   HetHet  IBS0    Kinship
##  28      3       117     1       2360618 0.143   0.0267  0.1356
##  28      3       117     2       2352628 0.161   0.0009  0.2441
##  28      3       117     3       2354540 0.120   0.0624  -0.0119
##  28      3       1344    1       2361807 0.093   0.1095  -0.2295
##  28      3       1344    12      2367180 0.094   0.1091  -0.2225
```

###### convert to matrix with king2mat (GENESIS package)
* **input**: `.kin` and `.kin0` and `iid`
* **command**: `king2mat`
* **output**: matrix

`iid` = text file containing iids in order from GenotypeData

```R
# read individual IDs from GenotypeData object
iids <- getScanID(genoData)
head(iids)

# create matrix of KING estimates
KINGmat <- king2mat(file.kin0 = system.file("wrkdir", "data.kin0", package="GENESIS"), 
                    file.kin = system.file("wrkdir", "data.kin", package="GENESIS"), 
                    iids = iids)
```

```R
# sample output

KINGmat[1:5,1:5]

##           NA19919 NA19916 NA19835 NA20282 NA19703
##  NA19919  0.5000 -0.0009 -0.0059 -0.0080  0.0014
##  NA19916 -0.0009  0.5000 -0.0063 -0.0150 -0.0039
##  NA19835 -0.0059 -0.0063  0.5000 -0.0094 -0.0104
##  NA20282 -0.0080 -0.0150 -0.0094  0.5000 -0.0134
##  NA19703  0.0014 -0.0039 -0.0104 -0.0134  0.5000
```

##### with SNPRelate
###### prefered since it can be done without leaving R
The vignette states: "Alternative to running the KING software, the `snpgdsIBDKING` function from the `SNPRelate` package can be used to calculate the KING-robust estimates directly from a GDS file. The ouput of this function contains a matrix of pairwise estimates, which can be used by the `GENESIS` functions" **this is a lie** You must extract the matrix and prepend the IIDs as row and column names.

Input: `mygenotype.gds`
Command: `snpgdsIBDKING`
Output: matrix of pairwise estimates

Options: 
**type** ("KING-robust" - for admixed pop,"KING-homo" - for homogeneous pop), 
**sample.id** (choose samples subset; default all), 
**snp.id** (choose SNPs subset; default all), 
**autosome.only** (default TRUE), 
**maf** (to use the SNPs with ">=maf" only; default no threshold)
**family.id** (default NULL; all individuals treated as singletons. If provided, within- and between- family relationships are estimated differently), 
**verbose**

```r
# snpgdsIBDKING requires a gds object; you cannot just point command to a .gds file
# read in the GDS file you just generated and verify its class
gdsfile <- snpgdsOpen(paste(genotype,".gds",sep=""))
class(gdsfile) #check for "gds.class"
```
```r
# calculate KING IBD Kinship coefficients
ibd_king <- snpgdsIBDKING(gdsfile, type="KING-robust", verbose=TRUE)
```
```r
class(ibd_king)
# snpgdsIBDClass (5 elements)
names(ibd_king)
# [1] "sample.id" "snp.id"    "afreq"     "IBS0"      "kinship"
```
```r
# extract kinship matrix
KINGmat = as.matrix(ibd_king$kinship)

# check output
KINGmat[1:5,][,1:5]

##   0.5000000000 -0.004695793  0.001941412 -0.0009816093 -0.01903618
##  -0.0046957930  0.500000000 -0.009200767 -0.0070120462 -0.03430987
##   0.0019414117 -0.009200767  0.500000000 -0.0085147706 -0.01809720
##  -0.0009816093 -0.007012046 -0.008514771  0.5000000000 -0.02480543
##  -0.0190361844 -0.034309866 -0.018097203 -0.0248054274  0.50000000

#add row and column labels
rownames(KINGmat) <- c(ibd_king$sample.id)
colnames(KINGmat) <- c(ibd_king$sample.id)
```

```R
# SAGE2 output using SNPRelate

KINGmat[1:5,][,1:5]

##                CH30380      VA30171      VA30167       CH30357     VA70028
##  CH30380  0.5000000000 -0.004695793  0.001941412 -0.0009816093 -0.01903618
##  VA30171 -0.0046957930  0.500000000 -0.009200767 -0.0070120462 -0.03430987
##  VA30167  0.0019414117 -0.009200767  0.500000000 -0.0085147706 -0.01809720
##  CH30357 -0.0009816093 -0.007012046 -0.008514771  0.5000000000 -0.02480543
##  VA70028 -0.0190361844 -0.034309866 -0.018097203 -0.0248054274  0.50000000
```

### Running PC-AIR
Uses pairwise measure sof kinship and ancestry divergence to determine the unrelated and representative subset for analysis. 

The KING-robust estimates are always used as measures of ancestry divergence for unrelated pairs of individuals; can also be used as measures of kinship for relatives (NOTE: they may be biased measures of kinship for admixed relatives with different ancestry)

###### Input:
input | description
------|------
`genoData` | `GenotypeData` class object
`kinMat` | matrix of pairwise kinship coefficient estimates (KING-robust estimates or other source)
`divMat` | matrix of pairwise measures of ancestry divergence (KING-robust estimates)

```R
# run PC-AiR
mypcair <- pcair(genoData = mygenoData, kinMat = KINGmat, divMat = KINGmat)
```
You should see the following verbage:

```
    Partitioning Samples into Related and Unrelated Sets...
    Unrelated Set: 1665 Samples 
    Related Set: 320 Samples
    Running Analysis with 748665 SNPs - in 75 Block(s)
    Computing Genetic Correlation Matrix for the Unrelated Set: Block 1 of 75 ...
    ...
    Computing Genetic Correlation Matrix for the Unrelated Set: Block 75 of 75 ...
    Performing PCA on the Unrelated Set...
    Predicting PC Values for the Related Set: Block 1 of 75 ...
    ...
    Predicting PC Values for the Related Set: Block 75 of 75 ...
    Concatenating Results...
```

###### Output: 

```R
summary(mypcair)
```

```
    Call:
    pcair(genoData = myGenoData, kinMat = KINGmat, divMat = KINGmat)

    PCA Method: PC-AiR 

    Sample Size: 1985 
    Unrelated Set: 1665 Samples 
    Related Set: 320 Samples 

    Kinship Threshold: 0.02209709 
    Divergence Threshold: -0.02209709 

    Principal Components Returned: 20 
    Eigenvalues: 11.286 2.216 1.95 1.924 1.914 1.866 1.849 1.837 1.815 1.797 ...

    MAF Filter: 0.01 
    SNPs Used: 644529
```

###### Options

using a reference population alongside sample; perhaps to determine which PCs capture which ancestral groups

```r
    pcair(genoData = mygenoData, unrel.set = IDs) # IDs of individuals from ref panel included in mygenoDate

    pcair(genoData = mygenoData,  kinMat = KINGmat, divMat = KINGmat, unrel.set = IDs) # IDs of individuals from ref panel included in mygenoDate; partition IDs first, then sample
```

###### Plotting PC-Air PCs

`plot` method provided by `GENESIS` package. Each point represents one individual. Visualization of population structure to **identify clusters of individuals with similar ancestry**. Can by altered by standard `plot` function manipulation. [Basis](https://www.rdocumentation.org/packages/graphics/versions/3.4.0/topics/plot) and [More Details](http://www.statmethods.net/advgraphs/parameters.html)

default: black dots = unrelated subset**;** blue pluses = related subsets

```r
# plot top 2 PCs
plot(mypcair)

# plot PCs 3 and 4
plot(mypcair, vx = 3, vy = 4)
```

### Running PC-Relate
Provides **genetic relatedness estimates**. Uses top PCs from PC-Air (ancestry capturing components) to adjust for population structure & individual ancestry in sample.

###### Input:

input | file | description
------|------|------
`training.set` | `mypcair`$`unrels` | vector of IIDs specifying unrelated subset to be used for ancestry adjustment per SNP
`genoData` | `mygenoData` | GenotypeData class object
`pcMat` | `mypcair`$`vectors[,1:n]` | matrix; columns are PCs 1 - n

```r
# run PC-Relate
mypcrelate <- pcrelate(genoData = HapMap_genoData, pcMat = mypcair$vectors[,1:2], training.set = mypcair$unrels)
```

You should see the following verbiage:
```
    Running Analysis with 748665 SNPs - in 75 Block(s)
    Running Analysis with 1985 Samples - in 1 Block(s)
    Using 2 PC(s) in pcMat to Calculate Adjusted Estimates
    Using 1665 Samples in training.set to Estimate PC effects on Allele Frequencies
    Computing PC-Relate Estimates...
    ...SNP Block 1 of 75 Completed - 7.165 mins
    ...
    ...SNP Block 75 of 75 Completed - 5.876 mins
    Performing Small Sample Correction...
```

###### Output:
* `write.to.gds = FALSE` = pcrelate obj (default)
* `write.to.gds = TRUE` = gds file `tmp_pcrelate.gds`

to read `tmp_pcrelate.gds`: 
```r
packages(gdsfmt)
mypcrelate <- openfn.gds("tmp_pcrelate.gds")
```

###### to parse PCRelate outputs:

command | description
--------|--------
`pcrelateReadKinship` | make a table of pairwise relatedness estimates
`pcrelateReadInbreed` | make a table of individual inbreeding coeficients
`pcrelateMakeGRM` | make a genetic relatedness matrix

option | description
-----|-----
`pcrelObj` | output from pcrelate; either a class pcrelate object or a GDS file
`scan.include` | vector of individual IDs specifying which individuals to include in the table or matrix; default NULL
`kin.thresh` | minimum kinship coefficient value to include in the table
`f.thresh` | minimum inbreeding coefficient value to include in the table
`scaleKin` | factor to multiply the kinship coefficients by in the GRM; default 2

```r
# make pairwise relatedness estimates table
relatepairs.tbl <- pcrelateReadKinship(pcrelObj = mypcrelate, kin.thresh = 2^(-9/2))

# make inbreeding coefficient table
inbreedcoef.tbl <- pcrelateReadInbreed(pcrelObj = mypcrelate, f.thresh = 2^(-11/2))

# make GRM
mygrm <- pcrelateMakeGRM(pcrelObj = mypcrelate, scan.include = iids[1:5], scaleKin = 2)
```

check results
```r
# grm output

mygrm[1:5,][,1:5]

##               CH30380      VA30171      VA30167      CH30357       VA70028
##  CH30380 0.9921236602  0.001587362  0.004589604  0.002636372  0.0008911428
##  VA30171 0.0015873618  1.006070553  0.002143034  0.001831966 -0.0025865272
##  VA30167 0.0045896042  0.002143034  1.002365658 -0.005183818  0.0049334393
##  CH30357 0.0026363721  0.001831966 -0.005183818  1.003906724  0.0062427112
##  VA70028 0.0008911428 -0.002586527  0.004933439  0.006242711  1.0005800629

quantile(mygrm)

##             0%           25%           50%           75%          100% 
##  -3.260058e-02 -3.014012e-03 -5.799979e-05  2.950375e-03  1.169809e+00
```
save files
```r
# for .csv output: add sep=',' and change ".txt"

write.table(mygrm, paste("GENESIS_Kincoef_matrix_",genotype,date,".txt",sep=""), row.names = F, quote = F)
write.table(relatepairs.tbl.nothresh, paste("GENESIS_relatedpairs_",genotype,date,".txt",sep=""), row.names = F, quote = F)
write.table(inbreedcoef.tbl.nothresh, paste("GENESIS_inbreedcoef_",genotype,date,".txt",sep=""), row.names = F, quote = F)
```

note: to look at just the first 5 rows and first 5 columns of matrix
```bash
# in shell
cut -d' ' -f 1-5 test.file | head -5 -
```
```r
# in r
test.file[1:5,][,1:5]
```

## \# QED \#
