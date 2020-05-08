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

## Data filtering
This process is made for trimming in loop several RNA-Seq fq.gz files in HPC. It is important to evaluate the pertinence to perform this step, because severe trimming can affect the outputs of expression in downstream analyses. If data is in good quality, this step could be avoided. A good discussion about it can be found in next papers and forums 

```
#!/bin/bash
#SBATCH -D ./
#SBATCH -o Trimm.out
#SBATCH -e Trimm.err
#SBATCH -J Trimmo
#SBATCH -n 32
#SBATCH -p normal


##module
module load software/bioinformatics/trimmomatic/0.36

##Tool

for f1 in *1.fq.gz
do
    f2=${f1%%1.fq.gz}"2.fq.gz"

TrimmomaticPE -phred33 -trimlog TrimLog $f1 $f2 trimmed."$f1".1P.fq.gz trimmed."$f1".1U.fq.gz trimmed."$f2".2P.fq.gz trimmed."$f2".2U.fq.gz ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 HEADCROP:12 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:145
done

```




