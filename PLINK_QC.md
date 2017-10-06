# Plink QC Pipeline
### Pagé Goddard
##### Oct 6 2017
---

## Resources
[PLINK summary statistic commands](http://zzz.bwh.harvard.edu/plink/summary.shtml)

[COG-Genomics PLINK command list](https://www.cog-genomics.org/plink/1.9/data)

[Tufts QC Vignette](http://sites.tufts.edu/cbi/files/2013/01/GWAS_Exercise5_QC.pdf)

[PMC3066182](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3066182/) Turner, Stephen et al. “Quality Control Procedures for Genome Wide Association Studies.” Current protocols in human genetics / editorial board, Jonathan L. Haines ... [et al.] CHAPTER (2011): Unit1.19. PMC. Web. 6 Oct. 2017.

## Basics of Quality Control (QC)
 *why we care:* With GWAS, hundreds of thousands of genotypes are generated so even a small percentage of genotyping error can lead to spurious GWAS results. This tutorial focuses on **downstream QC** (i.e. data cleaning after you have the genotype calls).

##### Two parts of downstream QC

 1. subject / sample-based
 2. variant / snp-based

**Sample QC Concerns**

term | desc | typical threshold
-----|------|------
sample-specific missingness rate | proportion of missing genotypes per individual | 
gender discordance | check that self-reported gender matches genotyped gender | 
cryptic relatedness | undisclosed familial relationships; duplicate enrollment | 
replicate discordance | agreement with independent genotyping | 
population outliers | subjects with significantly different genetic background | 

**SNP QC Concerns**

term | desc | typical threshold
-----|------|------
SNP-specific missingness rate | proportion of failed genotype assays per variant | 
minor allele frequency | low freq alleles more likely to represent genotyping error | 0.05
replicate discordance | agreement with independent genotyping | 
Hardy-Weinberg equilibrium | | 
Mendelian errors | in family data evidence of non-Mendelian transmission | N/A

## Example Plink command
```bash

plink --noweb --bfile inputfile --remove removeme.txt --make-bed --out outputfile

# basic command if all genotype data is in one file
```
* `plink` - calls plink in shell environment
* `--no-web` - usually not necessary; tells PLINK not to connect to the internet (which makes it slow)
* `--bfile` - tells plink to read fyour binary files `inputfile.bim`, `inputfile.fam`, `inputfile.bed`
* `--remove` - tells plink to remove all IDs listed in the named file
* `--make-bed` - write the outfile in our favorite `.bed`, `.bim`, `.fam` form
* `--out` - write my outfile with the following prefix
```bash
for ((i=1;i<=22;i++))
do 
plink --noweb --bfile inputfile_chr$i --remove removeme.txt --make-bed --out outputfile_chr$i
done

# if geno data is split by chromosome
```

* `for ((i=1;i<=22;i++))` - "for every value of `i` between 1 and 22, run the following script;" in the plink script, we then tell bash that the `i` refers to chromosome number in the file name using `filename_chr$i`; this is useful if your genotype data is divided by chromosome
* `--noweb` - not necessary
* `--bfile` - read from binary files with the prefix `inputfile_chr$i` where `$i` is the chromosome number from your `for` loop; your bfiles are your `.bim`, `.bed`, and `.fam` files where bim and fam are files contianing IDs for the SNPs and Individuals and .bed is the genotype file
* `--remove` - remove all IDs listed in the named file (in this case, the file removeme); there are a ton of different commands to use here, depending on what you want to do. A typical pipeline example can be found below.
* `--make-bed` - write the outfiles in as `.bed`, `.bim`, `.fam`
* `--out` - with the following prefix

## Some Plink command options for QC
This options replace the `--remove filename` option

option | description | notes
-------|------
`--update-sex filename` | updates sex label for individuals in filename | expects file with `FID` `IID` `sex (1,2)`, no header
`--remove filename` | removes samples in list | expects file with `FID` `IID`, no header
`--geno 0.05` | filters SNPs with genotyping frequency below 95% | to get a file listing the snps that remain after filtering, include `--write-snp.list`
`--mind 0.05` | exclude individuals with genotype rates below 95% | to get the list of the missingness rates include `--missing`
`--hwe 0.0001` | filters out SNPs with HWE exact test p-value below threshold | recommend setting a low threshold as major deviation (p-val of e-50, eg) is likely genotyping error while true SNP-trait association shows slight deviation
`--maf` | filters out SNPs with minor allele freq below threshold | default 0.01
`--genome --min 0.025` | identity by descent calculation to determine relatedness coefficient and remove individuals with values <0.025?? | These calculations are not LD-sensitive. It is usually a good idea to perform some form of LD-based pruning before invoking them.



## Log files
* every plink command has an automatic log file output: `outfileprefix.log`
* **number of SNPs/Samples loaded** and **number that passed QC**
* errors, warnings if applicable

*sample log file:*
```bash
PLINK v1.90b3.29 64-bit (24 Dec 2015)
Options in effect:
  --bfile sage_imputedgeno_gwas_171004_no21plus_chr1
  --maf 0.05
  --make-bed
  --noweb
  --out sage_imputedgeno_gwas_171004_maf05_finalQC_chr1

Hostname: burchardlab.ucsf.edu
Working directory: /media/BurchardRaid01/LabShare/Home/pgoddard/telo_wd_171004
Start time: Wed Oct  4 15:01:38 2017

Note: --noweb has no effect since no web check is implemented yet.
Random number seed: 1507154498
257655 MB RAM detected; reserving 128827 MB for main workspace.
1967966 variants loaded from .bim file.                              # SNP input 
1715 people (822 males, 890 females, 3 ambiguous) loaded from .fam.  # Sample input
Ambiguous sex IDs written to
sage_imputedgeno_gwas_171004_maf05_finalQC_chr1.nosex .
Using 1 thread (no multithreaded calculations invoked.
Before main variant filters, 1715 founders and 0 nonfounders present.
Calculating allele frequencies... done.
1387541 variants removed due to minor allele threshold(s)           # action taken
(--maf/--max-maf/--mac/--max-mac).
580425 variants and 1715 people pass filters and QC.                # SNP/Sample output
Note: No phenotypes present.
--make-bed to sage_imputedgeno_gwas_171004_maf05_finalQC_chr1.bed +
sage_imputedgeno_gwas_171004_maf05_finalQC_chr1.bim +
sage_imputedgeno_gwas_171004_maf05_finalQC_chr1.fam ... done.

End time: Wed Oct  4 15:01:40 2017
```

# Example Run
## Data locations

* imputed genotype data:
`/media/BurchardRaid01/LabShare/Home/dhu/sage_gwas/raw_snpid_updated/sage_imputed_mis_rsq03_snps_only_snpid_updated_chr$i`

```bash
ls -1 /media/BurchardRaid01/LabShare/Home/dhu/sage_gwas/raw_snpid_updated/
# for every chr: log, nosex, raw, binary plink files (bim bed fam)

# Geno_sage_imputed_mis_rsq03_snps_only_mex_chr10.log       # misc. PLINK output files
# Geno_sage_imputed_mis_rsq03_snps_only_mex_chr10.nosex
# Geno_sage_imputed_mis_rsq03_snps_only_mex_chr10.raw
# Geno_sage_imputed_mis_rsq03_snps_only_mex_chr11.log
# Geno_sage_imputed_mis_rsq03_snps_only_mex_chr11.nosex
# Geno_sage_imputed_mis_rsq03_snps_only_mex_chr11.raw
# Geno_sage_imputed_mis_rsq03_snps_only_mex_chr12.log
# Geno_sage_imputed_mis_rsq03_snps_only_mex_chr12.nosex
# Geno_sage_imputed_mis_rsq03_snps_only_mex_chr12.raw
# Geno_sage_imputed_mis_rsq03_snps_only_mex_chr13.log
# Geno_sage_imputed_mis_rsq03_snps_only_mex_chr13.nosex
# Geno_sage_imputed_mis_rsq03_snps_only_mex_chr13.raw
...
# sage_imputed_mis_rsq03_snps_only_snpid_updated_chr1.bed   # genotype files by chr
# sage_imputed_mis_rsq03_snps_only_snpid_updated_chr1.bim
# sage_imputed_mis_rsq03_snps_only_snpid_updated_chr1.fam
# sage_imputed_mis_rsq03_snps_only_snpid_updated_chr1.log
# sage_imputed_mis_rsq03_snps_only_snpid_updated_chr1.nosex
# sage_imputed_mis_rsq03_snps_only_snpid_updated_chr20.bed
# sage_imputed_mis_rsq03_snps_only_snpid_updated_chr20.bim
# sage_imputed_mis_rsq03_snps_only_snpid_updated_chr20.fam
# sage_imputed_mis_rsq03_snps_only_snpid_updated_chr20.log
```

* up-to-date sex reference:
`/media/BurchardRaid01/LabShare/Home/azeiger/Telomere/SAGE/Project/SAGE_updatedSex_GWAS.txt`
```bash
sexupdated='/media/BurchardRaid01/LabShare/Home/azeiger/Telomere/SAGE/Project/SAGE_updatedSex_GWAS.txt'

head -3 $sexupdated # 1,2 number of X chromosomes
# FID     IID     Sex
# 1000    1000    2
# 1001    1001    1
```

* individuals with saliva geno data to remove: `/media/BurchardRaid01/LabShare/Home/azeiger/Telomere/SAGE/Project/SAGE_salivaDNAexclude_082416.txt`
```bash
removeme_saliva="/media/BurchardRaid01/LabShare/Home/azeiger/Telomere/SAGE/Project/SAGE_salivaDNAexclude_082416.txt"

head -3 $removeme_saliva
# FID     IID
# VA70051 VA70051
# VA70040 VA70040
```
* old individuals to remove: `/media/BurchardRaid01/LabShare/Home/azeiger/Telomere/SAGE/Project/Telomere_remove_over21.txt` 
```bash
removeme_old="/media/BurchardRaid01/LabShare/Home/azeiger/Telomere/SAGE/Project/Telomere_remove_over21.txt"

head -3 $removeme_old
# FID     IID
# 1021    1021
# 1027    1027
```
* **note:** depending on your analysis, you can choose to keep or remove any subset of individuals; removing saliva samples is always recommended as it is prefered to work with genotypes sequenced from whole blood

##### If you want to merge all chromosomes into one file
```bash
### Concatenate genotype data

plink --bfile $datadir/$sagedata --chr 1-22 --make-bed --out SAGEbase_081517

# the next step would then look like:

###1. Updating Sex in .fam file
plink ---noweb -bfile SAGEbase_081517 --update-sex $sexupdated --make-bed --out ${out}_sex_chr
    # no for loop
    # no $i as we no longer need to call each chr individually
    # this step makes your QC run slower but your directory will look cleaner
```

## Pipeline
This pipeline will walk you through the steps used to QC the imputed SAGE2 genotype data for the Telomere project. We did not merge chromosomes

**set variables**

* in bash, we can define variables in the environment as `var='value'` to call on later using `$var`
* **useful shorthand**, especially when working with cumbersome file paths or bash scripting (which will be a different tutorial); think of it as **nicknaming your data**
* I prefer this approach because I **know where everything is** coming from and going to and what it is called **without having to parse** my script; it is also **easier to adapt** the script to new data because you only need to update the variable rather than every line of the script.

```bash
## Set up environment

# values
date=171004

# directories
wrkdir="/media/BurchardRaid01/LabShare/Home/pgoddard/wrkdir" #choose your working directory
datadir="/media/BurchardRaid01/LabShare/Home/dhu/sage_gwas/raw_snpid_updated" #identify where your genotype data are

# reference files
sagedata="sage_imputed_mis_rsq03_snps_only_snpid_updated_chr" #to run it will be ${sagedata}${i}
sexupdated="/media/BurchardRaid01/LabShare/Home/azeiger/Telomere/SAGE/Project/SAGE_updatedSex_GWAS.txt"
removeme_saliva="/media/BurchardRaid01/LabShare/Home/azeiger/Telomere/SAGE/Project/SAGE_salivaDNAexclude_082416.txt"
removeme_old="/media/BurchardRaid01/LabShare/Home/azeiger/Telomere/SAGE/Project/Telomere_remove_over21.txt"

# output files
out="sage_imputedgeno_gwas_${date}"

# move into working directory
cd $wrkdir
```

##### 1. Make sure sex is up-to-date
```bash
###1. Updating Sex in .fam file

for ((i=1;i<=22;i++))
do plink ---noweb -bfile $datadir/$sagedata$i --update-sex $sexupdated --make-bed --out ${out}_sex_chr$i
done

# prints log to screen; look for thes lines:
# --update-sex: 1987 people updated, 130 IDs not present.
# 332237 variants and 1990 people pass filters and QC.

# the log is also written to the ${out}_sex_chr$i.log files so don't worry about losing the information on the screen
```

##### 2. Remove saliva samples
```bash
for ((i=1;i<=22;i++))
do plink --noweb --bfile ${out}_sex_chr$i --remove $removeme_saliva --make-bed --out ${out}_nosaliva_chr$i
done

# 332237 variants and 1954 people pass filters and QC.
```

##### 3: Remove individuals over 21
```bash
for ((i=1;i<=22;i++))
do plink --noweb --bfile ${out}_nosaliva_chr$i --remove $removeme_old --make-bed --out ${out}_no21plus_chr$i
done

# 332237 variants and 1715 people pass filters and QC.
```

##### 4: Filtered out SNPs with genotyping efficiency below 95%
```bash
for ((i=1;i<=22;i++))
do plink --noweb --bfile ${out}_no21plus_chr$i --geno 0.05 --make-bed --out ${out}_geno05_chr$i
done

# 0 variants removed due to missing genotype data (--geno)
# 332237 variants and 1715 people pass filters and QC.
```

##### 5: Filtered out individuals with genotyping efficiency below 95%
```bash
for ((i=1;i<=22;i++))
do plink --noweb --bfile ${out}_geno05_chr$i --mind 0.05 --make-bed --out ${out}_mind05_chr$i
done

# 0 people removed due to missing genotype data (--mind)
# 332237 variants and 1715 people pass filters and QC.
```

##### 6: Screen for Cryptic relatedness
```bash
for ((i=1;i<=22;i++))
do plink --noweb --bfile ${out}_mind05_chr$i --genome --min 0.025 --make-bed --out ${out}_decrypted_chr$i
done

# 1954 people (906 males, 1044 females, 4 ambiguous) loaded from .fam.
# 1967966 variants and 1954 people pass filters and QC.
```

##### 7: Remove SNPs with MAF < 0.05
```bash
for ((i=1;i<=22;i++))
do plink --noweb --bfile ${out}_decrypted_chr$i --maf 0.05 --make-bed --out ${out}_maf05_chr$i
done

# 230023 variants removed due to minor allele threshold(s)
# 580425 variants and 1715 people pass filters and QC.
```

##### 8: Filtered SNPs that fail a HWE cutoff p<0.0001
```bash
for ((i=1;i<=22;i++))
do plink --noweb --bfile ${out}_maf05_chr$i --hwe 0.0001 --make-bed --out ${out}_hwe0001_chr$i
done

# 195 variants removed due to Hardy-Weinberg exact test.
```

##### clean up space
```bash
# make a separate directory for each QC stage and move the appropriate files into each
# be sure to leave the last set of QC files in your working directory for easy access

mkdir QC1_update_sex && mv ${out}_sex_chr* QC1_update_sex
mkdir QC2_remove_saliva && mv ${out}_nosaliva_chr* QC2_remove_saliva
mkdir QC3_remove_over21 && mv ${out}_no21plus_chr* QC3_remove_over21
mkdir QC4_genotype_efficiency && mv ${out}_geno05_chr* QC3_genotype_efficiency
mkdir QC5_individ_efficiency && mv ${out}_mind05_chr* QC4_individ_efficiency
mkdir QC6_cryptic_relatedness && mv ${out}_decrypted_chr* QC5_cryptic_relatedness
mkdir QC7_maf_05 && mv ${out}_maf05_chr* QC7_MAF_05

ls -1 $wrkdir
# QC1_update_sex
# QC2_remove_saliva
# QC3_remove_over21
# QC4_genotype_efficiency
# QC5_individ_efficiency
# QC6_cryptic_relatedness
# QC7_maf_05
```

##### 9: Subset into two populations: male controls, female controls

```r
#R work: Select subjects to keep for analysis (locally in R)

setwd("/media/BurchardRaid01/LabShare/Home/pgoddard/telo_wd_171004")

# partition by sex and case/control status
pheno <- read.csv("/media/BurchardRaid01/LabShare/Home/azeiger/Telomere/SAGE/sage2_clean2016_02_23_de_ident.csv", header=T)
telodat <- read.csv("raw_data/tel_res_MQ_SAGE2_09252017_FINAL.csv", header=T)
telodat2 <- merge(pheno[,c("SubjectID", "Male")], telodat, by.x="SubjectID", by.y="SampleID")

fm_cntl <- telodat2[telodat2$Male=="Female" & telodat2$Status=="control",]
m_cntl <- telodat2[telodat2$Male=="Male" & telodat2$Status=="control",]

    length(telodat$SampleID) # 1540
    length(fm_cntl$SubjectID) # 336
    length(m_cntl$SubjectID) # 260

# keep individuals with no missing covariates
# covariate file generated with covar script: /Dropbox/Telomeres/script_telo_covars_171005.R
covars <- read.table("tel_phenocovars_allSage2_100417.txt", header=T)
nomisscovar <- na.omit(covars)

fm_cntl_clean <- fm_cntl[fm_cntl$SubjectID %in% nomisscovar$FID,]
m_cntl_clean <- m_cntl[m_cntl$SubjectID %in% nomisscovar$FID,]

    length(fm_cntl_clean$SubjectID) # 180
    length(m_cntl_clean$SubjectID) # 139


# create subset ID lists for PLINK
keep_female <- data.frame(fm_cntl_clean$SubjectID, fm_cntl_clean$SubjectID)
keep_male <- data.frame(m_cntl_clean$SubjectID, m_cntl_clean$SubjectID)

head(keep_female)
#   BP70001 BP70001
#   BP70002 BP70002
#   BP70004 BP70004
#   BP70005 BP70005
#   BP70006 BP70006

# save subset ID list as txt file to wd
write.table(keep_female, "keep_female_171006.txt", row.names=F, col.names=F, quote=FALSE, sep=" ")
write.table(keep_male, "keep_male_171006.txt", row.names=F, col.names=F, quote=FALSE, sep=" ")
```

```bash
# PLINK work

## Plink command to extract female controls (n=180): 
for ((i=1;i<=22;i++))
do plink --bfile ${out}_maf05_finalQC_chr$i --keep keep_female_${date}.txt --make-bed --out ${out}_female_controls_chr$i
done

# check
wc -l ${out}_female_controls_chr22.fam
# 165 
# 15 female controls with all covariates removed by QC

## Plink command to extract male controls (n=139):
for ((i=1;i<=22;i++))
do plink --noweb --bfile ${out}_maf05_finalQC_chr$i --keep keep_male_${date}.txt --make-bed --out ${out}_male_controls_chr$i
done

# check
wc -l ${out}_female_controls_chr22.fam
# 130
# 9 male controls with all covariates removed by QC
```

##### clean up space
```bash
# finish clean up
mkdir QC8_MAF_05 && mv ${out}_maf05_finalQC_chr* QC8_maf_05
mkdir QC8_split_fm_cntl && mv ${out}_female_controls_chr* QC8_split_fm_cntl
mkdir QC8_split_m_cntl && mv ${out}_male_controls_chr* QC8_split_m_cntl
```

##### Final QC Stats

```bash
# Log summary
# --update-sex: 1987 people updated, 130 IDs not present.
# 332237 variants and 1990 people pass filters and QC.
# Removed 36 people with saliva data
# 0 variants removed due to missing genotype data (--geno)
# 0 people removed due to missing genotype data (--mind)
# 195 variants removed due to Hardy-Weinberg exact test.
# 0 people removed due to cryptic relatedness
# Removed 239 people over 21
# 230023 variants removed due to minor allele threshold(s)
# extracted controls and subset by sex

# After plink QC:
# 102019 variants and 1715 people pass filters and QC

# After downscaling to samples with telomere data:
# total: 545
# fm: 307
# m: 238
```

###### Get summary counts per step
```bash
# sum line count for each chromosome .bim file
# if data is not split by chr, you can do wc -l path/to/finalQCfile.bim
cd $wrkdir/QC_171004/
wc -l QC1_update_sex/${out}_sex_chr*.bim
wc -l QC2_remove_saliva/${out}_nosaliva_chr*.bim 
wc -l QC3_remove_over21/${out}_no21plus_chr*.bim
wc -l QC4_genotype_efficiency/${out}_geno05_chr*.bim 
wc -l QC5_individ_efficiency/${out}_mind05_chr*.bim 
wc -l QC6_cryptic_relatedness/${out}_decrypted_chr*.bim
wc -l QC7_maf_05/${out}_maf05_chr*.bim
wc -l QC8_hwe/${out}_hwe0001_chr*.bim
```

####### SNPs

command   | removed      | remaining
----------|--------------|----------
initial   | -            | 25236191
geno      | 0            | 25236191
MAF       | 17711861     | 7524330
HWE       | 5154         | 7519176
**total** | **17717015** | **7519176**


####### Individuals

command   | removed | remaining
----------|---------|----------
*initial* | -       | 1990
saliva    | 36      | 1954
> 21      | 239     | 1715
mind      | 0       | 1715
cryptic   | 0       | 1715
cntl only | 1170    | 545
all covar | 250     | 295

* total: 295
* female: 165
* male: 130

\**`cntl only` = only only individuals without asthma; `all covar` = individuals with data for all relevant covariates*
