# OMICS data pipline based on miRDeep2

The aim of this repository is to provide a simple step-b-step guide for beginners to map and quantify miRNAs from public available datasets.
This guide is based on the linux environment (Ubuntu) and miRDeep2, which is a software package for identification of novel and known miRNAs in deep sequencing data. (https://www.mdc-berlin.de/content/mirdeep2-documentation?mdcbl%5B0%5D=/n-rajewsky%23t-data%2Csoftware%26resources&mdctl=0&mdcou=20738&mdcot=6&mdcbv=crsjgo3KpH2eVDwEmJ_-5lh5FYkn8dZh4PNU6NsBrTE)

**Steps:**
__1) Download raw reads__
__2) Identify miRNAs with miRDeep2__



We donwloaded the raw reads from the Gene Expression Omnibus (GEO https://www.ncbi.nlm.nih.gov/geo/), which is a public functional genomics data repository supporting MIAME-compliant data submissions.

## Here is an example for Sample GSM3305457:

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
