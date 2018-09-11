# Analysis of yeast WGS samples

This repository contains all code used for analyzing yeast samples of group Peccoud of Colorado State University. All scripts are tested with software dependencies listed in section `Software dependencies`. The raw data and reference files are delivered separately due to their large size. Scripts in this repository allow full reproduction of all delivered and discussed results. 

Directory structure reflects the types of performed analyses. Scripts for performing each step are placed under analysis-specific directories. Each step has its own `Results`-directory. Some of the steps are independent, while some depend on each other. Dependencies are listed in this `README` document under each section.

# Software dependencies
* FastQC - https://www.bioinformatics.babraham.ac.uk/projects/fastqc/
* Trimgalore! - https://www.bioinformatics.babraham.ac.uk/projects/trim_galore/
* cutadapt - https://cutadapt.readthedocs.io/en/stable/
* bwa - http://bio-bwa.sourceforge.net
* Picard - https://broadinstitute.github.io/picard/

# Analysis steps

Note that instructions for each step assume that working directory in the beginning of the step is the repository's root directory, i.e. the directory where this `README` file is located.

## FastQC before trimming
FastQC calculates and visualizes sequencing quality metrics, e.g. GC content distribution and sequencing adapter contamination. FastQC metrics can be used to identify issues in sequencing. Comparing metrics befor and after trimming step helps to determine if trimming was successful.
 
Run FastQC by invoking script:
```
cd FastQC
bash run_fastqc_before_trimming.sh
```
Results of FastQC run will be placed in `FastQC/Results/FastQC_before_trimming`. See example reports on FastQC website for interpretation.

## Trimming reads
Adaptive trimming removes possible adapter contamination and low-quality tales of reads. Trimming produces new set of fastq files to be used in downstream analyses. Trimming is performed with Trimgalore!. Trimgalore! also runs FastQC on trimmed files and stores FastQC results along with trimmed fastq files.

Run trimming by invoking script:
```
cd Trimming
bash trim_reads.sh
```

Trimmed set of fastq files will be stored in `Trimming/Results/Trimmed_fastq_files`. Downstream steps depending on fastq files will look for them in this directory.

## Aligning reads

Reads are aligned to reference sequence using bwa.

Build BWA index from reference fasta by running script:
```
cd Alignment
bash index_genome.sh
```
Index is stored along with the reference fasta file in `Reference_files/genome`

Invoke alignment by running script:
```
cd Aligment
bash align_samples.sh
```

Used bwa parameters:
```
Number of threads (-t): 10
```

Aligned bam files and their indices are stored in `Alignment/Results/Alignments`. Downstream steps depending on bam files will look for them in this directory.

## Quality metrics
Alignment of reads produces information for further inspection of sample and sequencing quality. In this step, Picard is used to assess the quality from bam giles. Picard functions will produce multiple result files corresponding to different types of metrics. See Picard documentation for detailed descriptions on each metric.

In this step, we we also mark duplicate reads with Picard and produce duplicate marked bam files. These bam files will be used for downstream analyses.

Invoke quality assessment by running script:
```
cd Quality_metrics
bash process_samples.sh
```

Picard result files will be stored to `Quality_metrics/Results`. Processed bam files for downstream analysis will be stored to `Processed_bams`

## CNV calling

### CNVnator
Invoke CNVnator analysis by running
```
cd CNV_calling
bash CNVnator/call_samples.sh
```

By default, CNVnator is run with bin size of 20. Bin size variable is set in the beginning of script `CNVnator/run_CNVnator.sh`.

Results will be stored to `CNV_calling/CNVnator/Results`


### Breakdancer
For running Breakdancer, you need to set paths and permissions for Breakdancer:
* Add Breakdancer *perl* directory, located in your breakdance installation root folder, to `PATH` environment variable.
* Set execution rights for perl script `bam2cfg.pl` in that directory: `chmod gu+x breakdancer/perl/bam2fcfg.pl`. Give the actual path to Breakdancer installation directory.

After setting up, invoke Breakdancer, including configuration and calling itself, by running:
```
cd CNV_calling
bash breakdancer/call_samples.sh
```
Results are stored in `CNV_calling/breakdancer/Results`.

Breakdancer calls all samples at the same time. Results of all samples are in the same table.


## De novo assemblies

De novo assemblies were performed with two tools, with SPAdes used for downstream analyses.

### SOAP denovo



### SPADes

## Assembly metrics (Quast)

## Subsampling series for 
