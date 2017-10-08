# Useful Bioinformatic Databases
## Pagé Goddard
### Oct 7, 2017

This is a dynamic document where I will be adding and annotating databases I come across with their data types, possible uses, and current limitations. If you have any databases that you use regularly or have found useful in the past, please send them my way and I'll add them here!

### Overview of all databases included:

### Overview of all databases included:
Database | data type | uses | limitations
---------|-----------|------|------------
UCSC Table browser | curated genomic annotations from multiple sources | find functional and sequence information by genomic location |
HPO Human Phenotype Ontology | Phenotypic information with associated genes (mined from OMIM, Orphanet, DECIPHER) | finding related phenotypes and genes given phenotypes
OMIM Online Mendelian Inheritance in Man | phenotype annotations and gene annotations | mendelian phenotypes with known/suspected genetic cause and disease genes with related phenotypes; includes summaries, references and hypotheses for gene function if unconfirmed | only contains monogenic diseases and related genes (but is super powerful for those)
[MGI](http://www.informatics.jax.org/) | Phenotype and disease ontologies

### Annotating SNPs
So you ran your GWAS and got some SNPs. Now we want to gauge their biological and clinical potential.

* snpinfo
* dbsnp
* Ensemble VEP
* UCSC Genome Browser rsid annotator

### Annotating Genes
Let's say you run a GWAS and identify a set of genes from your SNPs that you now want to investigate.

*Gene Function*

* Uniprot
* GeneCards
* NCBI Gene search

*Gene-phenotype links*

* MGI (http://www.informatics.jax.org/)
* RNAi database (http://www.genomernai.org/)
* OMIM
