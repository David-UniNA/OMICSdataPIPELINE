# OMICS Data Pipeline Based on miRDeep2

The aim of this repository is to provide a simple step-by-step guide for beginners to map and quantify miRNAs from publicly available datasets. We download raw reads from the [Gene Expression Omnibus](https://www.ncbi.nlm.nih.gov/geo/), a public functional genomics data repository. This guide is based on the Linux environment (Ubuntu version 22.04) and [miRDeep2](https://www.mdc-berlin.de/content/mirdeep2-documentation?mdcbl%5B0%5D=/n-rajewsky%23t-data%2Csoftware%26resources&mdctl=0&mdcou=20738&mdcot=6&mdcbv=crsjgo3KpH2eVDwEmJ_-5lh5FYkn8dZh4PNU6NsBrTE), a software package for the identification of novel and known miRNAs in deep sequencing data. The pipeline uses the 'adapter sequence' for the Illumina HiSeq 2000 platform but can generally be used for all platforms if the adapter sequence is known.

## Pipeline Steps
1. Download raw reads
2. Identify miRNAs with miRDeep2
3. Quantify miRNAs with miRDeep2

The following schema illustrates the workflow of the presented pipeline:
<br>
![image](https://github.com/user-attachments/assets/96ec3609-79e5-4dce-a368-c84611777881)

---

## Example for a Cell Line Dataset from Publicly Available Samples

This is a short guide to find a sequence read archive for an A375 cell line experiment based on sample GSM2475129.

1. Search the sample on GEO...<br>
   ![image](https://github.com/user-attachments/assets/167c9117-82c0-4b5f-901d-4f7feb2744c1)
2. Click on the SRA experiment (SRX2533381) to get more information...<br>
   ![image](https://github.com/user-attachments/assets/83d69921-49d0-43ed-8182-7a07f4e91d3f)
3. Open the SRA run...<br>
   ![image](https://github.com/user-attachments/assets/f6f421de-7a4c-44c6-bade-17ba11fc09a9)
4. Sequence Read Archive, which provides all information about the experiment run (SRR5224202).<br>
   ![image](https://github.com/user-attachments/assets/6a261779-fff1-4840-9b83-174d22d54bf2)
---

### Downloading FastQ File
Once we find the SRA run of interest, we use the [SRA toolkit](https://github.com/ncbi/sra-tools) to download the FastQ file of the experiment using the following command:
```bash
fastq-dump --split-files SRA_RUN
```
Replace `SRA_RUN` with the experiment run name (e.g., SRR5224202).

### Mapping miRNAs Using miRDeep2
Use the mapping function of miRDeep2 with the following command:
```bash
mirdeep2-master/src/mapper.pl SRA_RUN.fastq -e -h -i -j -l 18 -k CUT_ADAPT -m -p Bowtie/B -q -s SRA_RUNreads.fa -t SRA_RUNreadsVSgenome -v -n
```
For a detailed description of the syntax, refer to the [miRDeep2 documentation](https://www.mdc-berlin.de/content/mirdeep2-documentation?mdcbl%5B0%5D=/n-rajewsky%23t-data%2Csoftware%26resources&mdctl=0&mdcou=20738&mdcot=6&mdcbv=crsjgo3KpH2eVDwEmJ_-5lh5FYkn8dZh4PNU6NsBrTE). Replace `SRA_RUN.fastq` with the downloaded FastQ file. The CUT_ADAPT sequence for Illumina HiSeq 2000 is as follows:
```text
TGGAATTCTCGGGTGCCAAGG
```

### Quantifying Reads Using miRDeep2
The final step is quantifying reads using the quantifier function from miRDeep2:
```bash
mirdeep2-master/src/quantifier.pl -p hairpin.fa -m mature.fa -r SRA_RUNreads.fa -t hsa -k
```
Refer to the [miRDeep2 documentation](https://www.mdc-berlin.de/content/mirdeep2-documentation?mdcbl%5B0%5D=/n-rajewsky%23t-data%2Csoftware%26resources&mdctl=0&mdcou=20738&mdcot=6&mdcbv=crsjgo3KpH2eVDwEmJ_-5lh5FYkn8dZh4PNU6NsBrTE) for details. Here:
- `SRA_RUNreads.fa` is the output from the mapping step.
- [hairpin.fa](https://www.mirbase.org/download/hairpin.fa) contains Fasta sequences of all miRNA hairpins.
- [mature.fa](https://www.mirbase.org/download/mature.fa) contains Fasta sequences of all mature miRNA sequences.

For the presented case (SRR5224202), the results show a mapping of 62% and 10 reads per million (RPM). You can browse the A375 folder for the quantification outcome.

---

## Installation Guide for Ubuntu

### 1. [miRDeep2](https://github.com/rajewsky-lab/mirdeep2)
Download and unpack the miRDeep2 tool in the `Downloads` folder. If not already installed, manually install the [GCC compiler](https://linuxize.com/post/how-to-install-gcc-compiler-on-ubuntu-18-04/) using the following command:
```bash
sudo apt-get install build-essential
```
other useful packages:
```bash
sudo apt install ruby
sudo apt install curl
sudo apt update && sudo apt upgarde
```
To install miRDeep2, open a new terminal and run:
```bash
cd mirdeep2-master/
perl install.pl
```
It can be that you have to run 2 times the script to complete the installation. Ensure installation works by checking for tutorial messages in the end. 
To run the tutorial:
```bash
cd tutorial_dir/
bash run_tut.sh
```
There is usually no need to manually install [ViennaRNA](https://www.tbi.univie.ac.at/RNA/). If required, download the `.deb` file for Ubuntu 22.04 and install it with:
```bash
sudo dpkg -i viennarna_2.6.2-1_amd64.deb
```

### 2. [Bowtie](https://bowtie-bio.sourceforge.net/manual.shtml)
Install Bowtie, a short-read aligner, for aligning large DNA sequences to genomes. Download [Bowtie build 1.1.1](https://zoomadmin.com/HowToInstall/UbuntuPackage/bowtie) and run:
```bash
mirdeep2-master/essentials/bowtie-1.1.1/bowtie-build GRCh38.p14
```

### 3. [SRA Toolkit](https://github.com/ncbi/sra-tools) (ver. 3.0.7)
Install the SRA toolkit for dataset downloads using:
```bash
sudo apt install sra-toolkit
vdb-config -i
```
Set the cache folder (e.g., `Downloads/tmp`) and exit.

### 4. [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
FastQC is a quality control tool for sequencing data. Unzip the downloaded file in the `Downloads` folder and install Java if required:
```bash
sudo apt install default-jre
cd FastQC/
./fastqc
```

---
