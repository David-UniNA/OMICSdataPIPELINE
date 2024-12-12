# OMICS data pipline based on miRDeep2

The aim of this repository is to provide a simple step-b-step guide for beginners to map and quantify miRNAs from public available datasets. Therefore, we donwload raw reads from the [Gene Expression Omnibus](https://www.ncbi.nlm.nih.gov/geo/), which is a public functional genomics data repository. This guide is based on the linux environment (Ubuntu vers. 22-04) and [miRDeep2](https://www.mdc-berlin.de/content/mirdeep2-documentation?mdcbl%5B0%5D=/n-rajewsky%23t-data%2Csoftware%26resources&mdctl=0&mdcou=20738&mdcot=6&mdcbv=crsjgo3KpH2eVDwEmJ_-5lh5FYkn8dZh4PNU6NsBrTE), which is a software package for identification of novel and known miRNAs in deep sequencing data. The pipline uses the 'adapter sequence' for the Illumina HiSeq 2000 platform, but can generally used for all platforms if the adapter sequence is known.


**Pipline steps:**
1) Download raw reads
2) Identify miRNAs with miRDeep2
3) Quantify miRNAs with miRDeep2

The following schema illustrate the working-flow of the presented pipline.
<br>
![image](https://github.com/user-attachments/assets/96ec3609-79e5-4dce-a368-c84611777881)

_________________

## Here is an example for a cell line dataset from the public available sample:

Here is a short guide to find a sequence read archive for a A375 cell line experiment based on sample GSM3305457.

1. search the sample on GEO...
![image](https://github.com/user-attachments/assets/924b2312-1962-490c-9e24-c01c7ad5fdbc)
2. select the correct search result (item 4) and open it (https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM3305457)...
![image](https://github.com/user-attachments/assets/f77ebeae-a790-4cc5-8e2e-ce318b50bea3)
3. click on SRA number (SRX4456412) to get more information (https://www.ncbi.nlm.nih.gov/sra?term=SRX4456412)...
![image](https://github.com/user-attachments/assets/a418f7a4-8032-44a4-b945-7c847ff7c6f3)
4. open the SRA run...
![image](https://github.com/user-attachments/assets/d0176c2f-4db1-42ab-9a02-7fc0e0241f9d)
5. open Sequence Read Archive, which provide us with all information of the experiment run (https://trace.ncbi.nlm.nih.gov/Traces/?view=run_browser&acc=SRR7591172&display=metadata).
![image](https://github.com/user-attachments/assets/f3826c62-1832-453c-95c4-e25e65acf592)
_________________
### Once we found the SRA run we are interested in, we use the [SRA toolkit](https://github.com/ncbi/sra-tools) to download the fastq file of the experiment using the following command:
```
fastq-dump –split-files SRA_RUN
```
SRA_RUN should be changed according to the experiment run name (SRR7591172).

### Next we used the mapping function of miRDeep2 with the following command:
```
mirdeep2-master/src/mapper.pl SRA_RUN.fastq -e -h -i -j -l 18 -k CUT_ADAPT -m -p Bowtie/B -q -s SRA_RUNreads.fa -t SRA_RUNreadsVSgenome -v -n
```
The description of the syntax is described on the [miRDeep2 homepage](https://www.mdc-berlin.de/content/mirdeep2-documentation?mdcbl%5B0%5D=/n-rajewsky%23t-data%2Csoftware%26resources&mdctl=0&mdcou=20738&mdcot=6&mdcbv=crsjgo3KpH2eVDwEmJ_-5lh5FYkn8dZh4PNU6NsBrTE). SRA_RUN.fastq is the previously downloaded fastq file. CUT_ADAPT sequence for Illumina HiSeq 2000 is as followed:
```
TGGAATTCTCGGGTGCCAAGG
```
 
### Last step is the quantifing of reads using the quantifier function from miRDeep2 with the following command:
```
mirdeep2-master/src/quantifier.pl -p hairpin.fa -m mature.fa -r SRA_RUNreads.fa -t hsa -k
```
The description of the syntax is described on the [miRDeep2 homepage](https://www.mdc-berlin.de/content/mirdeep2-documentation?mdcbl%5B0%5D=/n-rajewsky%23t-data%2Csoftware%26resources&mdctl=0&mdcou=20738&mdcot=6&mdcbv=crsjgo3KpH2eVDwEmJ_-5lh5FYkn8dZh4PNU6NsBrTE). SRA_RUNreads.fa is the output from the mapping.
<br>[hairpin.fa](https://www.mirbase.org/download/hairpin.fa) is a Fasta format sequences of all miRNA hairpins.
<br>[mature.fa](https://www.mirbase.org/download/mature.fa) is a Fasta format sequences of all mature miRNA sequences.

For the presented case the result shows a mapping of 63% of 8 reads per million (RPM). You can browse in the A375 folder for the quantifying outcome.
_________________
_________________

## Installation help using Ubuntu:
1. [miRDeep2](https://github.com/rajewsky-lab/mirdeep2)
   Download the zip file of the whole miRDeep2 tool and unpack the file in Downloads.
   If not already installed on Ubuntu, manually install the [GCC compiler](https://linuxize.com/post/how-to-install-gcc-compiler-on-ubuntu-18-04/).
   Open a new terminal to install perl:
   ```
   cd mirdeep2-master/
   ```
   ```
   perl install.pl
   ```
   When the tutorial message appears the installation works!

   There should be no need to install [ViennRNA](https://www.tbi.univie.ac.at/RNA/) manually.
   If yes, download ViennaRNA for the version ubuntu 22.04 file deb and run the installation with the foollowing command:
   ```
   sudo dpkg -i viennarna_2.6.2-1_amd64.deb
   ```

2. [bowtie](https://bowtie-bio.sourceforge.net/manual.shtml)
   We install bowtie a shor read aligner to quickly align large sets of short DNA sequences to large genomes such as the ‘GRCh38p14’ human genome assembly. Therefore, we download [bowtie build 1.1.1](https://zoomadmin.com/HowToInstall/UbuntuPackage/bowtie) and run it with the following command:
   ```
   mirdeep2-master/essentials/bowtie-1.1.1/bowtie-build GRCh38.p14
   ```
      
4. [SRA toolkit](https://github.com/ncbi/sra-tools) (ver. 3.0.7):
   The SRA tool is needed to download the datasets. The command in terminal of Ubuntu to install SRA is as followed:
   ```
   sudo apt install sra-toolkit
   ```
   ```
   vdb-config -i
   ```
   Set the cache folder. In our case we have chosen 'Downloads/tmp' and exit the installation.

5. [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
   A quality control tool for high throughput sequence data, which can be used.
   Unzip the dowloaded file in Downloads and install Java if needed with:
   ```
   sudo apt install default-jre
   ```
   Before you start FastQC, change your directory from Downloads to the FastQC folder with:
   ```
   cd FastQC/
   ```
   run the pplication.
   ```
   ./fastqc
   ```
    
