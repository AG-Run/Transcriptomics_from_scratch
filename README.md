# Transcriptomics_from_scratch
This is a pipeline to perform data filtering, and mapping using RNA-Seq data in HPC enviroment running using slurm files

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

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |


[I'm an inline-style link](https://www.google.com)


