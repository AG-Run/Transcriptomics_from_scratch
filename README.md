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
This process is made for trimming and low quality removal data

```
#!/bin/bash
#SBATCH -D .
#SBATCH -o trimm_all_files.out
#SBATCH -e trimm_all_files.err
#SBATCH -J trimm_all_f
#SBATCH -n 3
#SBATCH -p gpu

module load software/bioinformatics/trimmomatic/0.36

time perl trimpoly_batch.pl AM.list;
```




