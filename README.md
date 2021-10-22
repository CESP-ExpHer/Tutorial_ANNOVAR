# Tutorial ANNOVAR
*Created by:* Yazdan Asgari<br>
*Creation date:* 21 Oct 2021<br>
*Update:* 21 Oct 2021<br>
https://cesp.inserm.fr/en/equipe/exposome-and-heredity
<br>
<br>
**Ref:** Wang K, Li M, Hakonarson H. "ANNOVAR: Functional annotation of genetic variants from next-generation sequencing data", *Nucleic Acids Research*, 38:e164, 2010, [Paper_link](https://pubmed.ncbi.nlm.nih.gov/20601685/)<br>
Hui Yang, Kai Wang, "Genomic variant annotation and prioritization with ANNOVAR and wANNOVAR", *Nat. Protoc.* 10(10): 1556–1566, 2015, doi: 10.1038/nprot.2015.105
[Paper_link](https://pubmed.ncbi.nlm.nih.gov/26379229/)
<br>
[ANNOVAR Website](https://annovar.openbioinformatics.org/en/latest/)
<br>
<br>
## Table of Contents

## How to use
Here is a brief tutorial on how to use "ANNOVAR" to annotate a list of SNPs.
<br>
- First, you need to download the latest version of ANNOVAR from its [website](https://annovar.openbioinformatics.org/en/latest/user-guide/download/) (registration required).<br>
- Then, extract it using the following command in the terminal (using **MobaXterm** in our sever). **NOTE:** You should change the **PATH** to where the downloaded folder exists. Here, we assume that the downloaded file name is **annovar.latest.tar.gz**.
```
tar xvfz annovar.latest.tar.gz
```
- After that, move your SNP file to the **"annovar"** folder (which was created after the extraction).
The SNP file should contain at least 5 columns including **"chromosome number"**, **"start"** and **"end"** positions (which are the same for a SNP), **"A0"** (Reference Allele which is available in Normal Human Genome Assembly), and **"A1"** (Alternate Allele). **NOTE:** The input data should **NOT** have any header. Here is an example:

|  |  |  |  |  | 
|:---:|:---:|:---:|:---:|:---:|  
| 4	| 95733906	| 95733906	| T	| G |
| 3	| 11604119	| 11604119	| G	| A |
| 18	| 45855051	| 45855051	| T	| C |
| 4	| 109106451	| 109106451	| A	| C |
| 4	| 24872381	| 24872381	| T	| C |

- Now, if you run the following command, it would annotate th input file based on the **Human Genome Assembly hg19**: 
```
perl annotate_variation.pl -out YOUR_OUTPUT_FILE_NAME -build hg19 YOUR_INTPUT_FILE_NAME humandb/
```
- **NOTE:** It is clear that you need to have perl on your OS to run ANNOVAR since it is written in perl programming language.
- After running, you have multiple files:
  - **.log** file: includes information regarding the running process
  - **.variant_function** file: includes annotation for each SNP
  - **.exonic_variant_function** file: includes detailed information for exonic SNPs
  - **.invalid_input** file: contains SNPs if they got error during the annotation process


### Some Annotation Tools 
There are some tools and web pages you could use for annotating a list of SNPs which some of their most famous methods are as follows:
- **Perl**: [**ANNOVAR**](https://annovar.openbioinformatics.org/en/latest/)
  - You could use this tools to perform the annotation in four different scenarios
     - Gene-based annotation
     - Region-based annotation
     - Filter-based annotation
     - Other functionalities
- **Java**: [**snpEff / SnpSift**](http://pcingola.github.io/SnpEff/)
- **R**: 
  - [**annotatr**](https://bioconductor.org/packages/release/bioc/html/annotatr.html): This package performs an annotation just in a range of positions (Not just a single base pair position), [Paper Link](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5860117/)
  - [**biomaRt**](https://bioconductor.org/packages/release/bioc/vignettes/biomaRt/inst/doc/accessing_ensembl.html): using getBM function
- [**Ensembl variant_effect_predictor**](https://www.ensembl.org/info/docs/tools/vep/index.html)
- [**Galaxy web-based platform**](https://usegalaxy.org/): You should use "snpEff" tool in the platform
