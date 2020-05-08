# Transcriptomics_from_scratch
This is a pipeline to perform data filtering, and mapping using RNA-Seq data in HPC enviroment using slurm files

## Download data using a FTP extension provided
If a FTP extension is provided by the sequencing center run the command

``` lftp -u X200ZC00000000-Z00_00_00_00_dLLL , ccCcccEe  -p 21 -d 000.000.00.000 set ssl:verify-certificate no ```

Where
| Parameter |     Description     | 
| --------- |:-------------------:| 
| -u        | Username, password  | 
| -p        | Port, for ftp use 21|
| -d        | URL for data download | 
| set ssl   | "no" for avoid a certificate request |  


## Data quality analysis using FASTQC
To determine the data quality run the command (slurm)

```
#!/bin/bash
#SBATCH -D /home/Biop_Temp/Download/Rawdata
#SBATCH -o FastQC.out
#SBATCH -e FastQC.err
#SBATCH -J FastQC
#SBATCH -p normal
#SBATCH -N 1
#SBATCH -n 32
#SBATCH -o /home/Biop_Temp/Download/Rawdata/fastqc.log

module load software/bioinformatics/fastqc/0.11.4

fastqc Y*/*.gz
```

## Data filtering using Trimmomatic
This process is made for trimming in loop several RNA-Seq fq.gz files in HPC. It is important to evaluate the pertinence to perform this step, because severe trimming can affect the outputs of expression in downstream analyses. If data is in good quality, this step could be avoided. A good discussion about it can be found in next papers: [MacManes, 2014](https://www.frontiersin.org/articles/10.3389/fgene.2014.00013/full) , [Liao & Shi, 2019](https://www.biorxiv.org/content/10.1101/833962v1) 

```
#!/bin/bash
#SBATCH -D ./
#SBATCH -o Trimm.out
#SBATCH -e Trimm.err
#SBATCH -J Trimmo
#SBATCH -n 32
#SBATCH -p normal

module load software/bioinformatics/trimmomatic/0.36

for f1 in *1.fq.gz
do
    f2=${f1%%1.fq.gz}"2.fq.gz"

TrimmomaticPE -phred33 -trimlog TrimLog $f1 $f2 trimmed."$f1".1P.fq.gz trimmed."$f1".1U.fq.gz trimmed."$f2".2P.fq.gz trimmed."$f2".2U.fq.gz ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 HEADCROP:12 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:145
done

```
Note:If trimming is performed, it is necessary to determine again the quality data assessment, please refer to FastQC command to run this step again.


## Mapping using Hisat2

This process is made for align the RNA-Seq to a reference genome. To run the mapping with Hisat2, two steps are followed.

1) Build the index of your reference genome
This process facilitates the aligning. Next is an example for Saccharomyces cerevisiae genome R64_SC.  

```
#!/bin/bash
#SBATCH -D ./
#SBATCH -o Hisatbuild.out
#SBATCH -e Hisatbuild.err
#SBATCH -J Hisatbuild
#SBATCH -n 32
#SBATCH -p normal

module load devtools/bioconda/bioconda3
source activate hisat2_env

hisat2-build -p 16 GCF_000146045.2_R64_genomic.fasta R64_hisat2

```
Where *p*: threads, *GCF_000146045.2_R64_genomic.fasta*: reference genome in fasta file, *R64_hisat2*: preffix of the index files.

2) Perform the mapping with Hisat2 tool
Run  next command for all RNA-Seq samples

```
#!/bin/bash
#SBATCH -D ./
#SBATCH -p normal
#SBATCH -N 1
#SBATCH -n 32
#SBATCH -o Mapeo.out
#SBATCH -e Mapeo.err
#SBATCH -J MapLev_allsamples
 
module load devtools/bioconda/bioconda3
source activate hisat2_env
     
hisat2 -p 30 -x /path to the index files/R64_hisat2 -1 forwared_file_1.fq.gz -2 reverse_file_2.fq.gz -S file_name.sam

```

## Conversion from .sam to bam files using Samtools
In this step, the ouptpu of hisat2 in .sam files will be transformed into .bam files to be analyzed further using the SeqMonk tool.

```
#!/bin/bash
#SBATCH -D ./
#SBATCH -p normal
#SBATCH -N 1
#SBATCH -n 32
#SBATCH -o Conv_sortbam.out
#SBATCH -e Conv_sortbam.err
#SBATCH -J Conv_to_sortbam

module load software/bioinformatics/samtools/1.5
     
samtools view -@ 1 -bS Y1_1_38.sam > Y1_1_38.bam
samtools sort Y1_1_38.bam -o Y1_1_38_sort.bam& 
samtools view -@ 1 -bS Y1_38.sam > Y1_38.bam
samtools sort Y1_38.bam -o Y1_38_sort.bam& 

```

## Analysis of diferencial expression using SeqMonk
The output of the conversion step are sort.bam files, these files are the input data for SeqMonk.
Nice [SeqMonk tutorials and manuals](https://www.bioinformatics.babraham.ac.uk/projects/seqmonk/).


Thanks for check this pipeline!

Regards
