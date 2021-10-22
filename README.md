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
## How to use ANNOVAR to annotate a list of SNPs
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

Here is an example of a **.variant_function** file:

|  |  |  |  |  |  |  |
|:---|:---|:---:|:---|:---:|:---:|:---:| 
| intronic	| SDF4	| 1	| 1156131	| 1156131	| T	| C |
| intergenic	| TTLL10(dist=1927),TNFRSF18(dist=3646)	| 1	| 1135242	| 1135242	| A	| C |
| UTR3	| TNFRSF18(NM_004195:c.311A>G)	| 1	| 1138913	| 1138913	| T | C |
| downstream	| LOC105378591(dist=1)	| 1	| 1980639	| 1980639	| G	| A |
| ncRNA_intronic	| LOC105378591	| 1	| 1981118	| 1981118	| A	| C |
| exonic	| DFFB	| 1	| 3800242	| 3800242	| A |	G |

Here is an example of a **.exonic_variant_function** file:
|  |  |  |  |  |  |  |  |
|:---|:---|:---|:---:|:---|:---|:---:|:---:| 
| line80	| synonymous SNV	| DFFB:NM_004402:exon7:c.A954G:p.P318P,	| 1	| 3800242	| 3800242	| A	| G |
| line96	| nonsynonymous SNV	| MASP2:NM_006610:exon9:c.G1111T:p.D371Y,	| 1	| 11090916	| 11090916	| C	| A |
| line105	| synonymous SNV	| MTOR:NM_004958:exon33:c.G4731A:p.A1577A,	| 1	| 11205058	| 11205058	| C	| T |
| line171	| nonsynonymous SNV	| CELA2B:NM_015849:exon4:c.G340A:p.D114N,	| 1	| 15808872	| 15808872	| G	| A |

If you want to merge two files (**.variant_function** and **.exonic_variant_function**), you could use the following **R script** to do that:
```r
# defining the path for your data
setwd("PATH_FOR_YOUR_ANNOTATION_FILES")

# reading the annotation data
annovar_input <- read.table(file = "YOUR_VARIANT_FUNCTION_DATA", sep = "")

# renaming the coulumns names
colnames(annovar_input) <- c("annovar_input_Type",	"annovar_input_Gene",	"annovar_input_chr_number",	
  "annovar_input_pos1",	"annovar_input_pos2",	"annovar_input_A0",	"annovar_input_A1")

# reading the exonic annotation data
annovar_input_exons <- read.table(file = "YOUR_EXONIC_VARIANT_FUNCTION_DATA", sep = "\t")

# renaming the coulumns names
colnames(annovar_input_exons) <- c("annovar_input_exons_line", "annovar_input_exons_Type",	"annovar_input_exons_Gene",	
                                   "annovar_input_exons_chr_number",	"annovar_input_exons_pos1",	"annovar_input_exons_pos2",	
                             "annovar_input_exons_A0",	"annovar_input_exons_A1")

# you need to install it if it is not installed before
library(dplyr)

# merging two data 
annovar_merge <- left_join(annovar_input, annovar_input_exons, 
                           by = c ("annovar_input_chr_number" = "annovar_input_exons_chr_number",
                                   "annovar_input_pos1" = "annovar_input_exons_pos1",
                                   "annovar_input_A0" = "annovar_input_exons_A0",
                                   "annovar_input_A1" = "annovar_input_exons_A1"))

# saving the output
write.table(annovar_merge, file = "YOUR_OUTPUT_FILE_NAME", sep = "\t",
            col.names = TRUE, row.names = FALSE, quote = FALSE)
```

And that is it! Your SNP list are now annotated.<br><br>
For more information about different options available in the analysis using ANNOVAR, please take a look at its [website](https://annovar.openbioinformatics.org/en/latest/user-guide/startup/)
### Some Annotation Tools 
We just talked about ANNOVAR in this tutorial. However, there are some other tools and web pages you could use for annotating a list of SNPs. Here, we provide some of their most famous methods based on the programming language they used:
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
