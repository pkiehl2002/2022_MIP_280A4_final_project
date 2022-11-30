# MIP 280A4 Final Project 2022

This report documents the final project I did for MIP 280A4, Microbial Sequence Analysis, in the fall of 2022.

It is written in [Markdown format](https://www.markdownguide.org/basic-syntax/).  

## Step 1: Thanksgiving Break

I'm going to eat some delicous food

## Step 2: Describe my project

My project is about x y z and my dataset is from a b c.

## Step 3: Quality Test
On Ubuntu:

conda activate bio_tools

cd /home/data_for_classes/2022_MIP_280A4/final_project_datasets

cp Paenibacillus_Illumina_R1.fastq ~/final_project_MIP280A4

cp Paenibacillus_Illumina_R2.fastq ~/final_project_MIP280A4/

cp Paenibacillus_Nanopore.fastq ~/final_project_MIP280A4/

cd ~/final_project_MIP280A4/

fastqc Paenibacillus_Illumina_R1.fastq

fastqc Paenibacillus_Illumina_R2.fastq

fastqc Paenibacillus_Nanopore.fastq


On Terminal:

cd .\Documents\

sftp pkiehl@thoth01.cvmbs.colostate.edu

cd final_project_MIP280A4

get Paenibacillus_Illumina_R1_fastqc.html

get Paenibacillus_Illumina_R2_fastqc.html

get Paenibacillus_Nanopore_fastqc.html


Results:

file:///C:/Users/pkiehl/Documents/Paenibacillus_Illumina_R1_fastqc.html

file:///C:/Users/pkiehl/Documents/Paenibacillus_Illumina_R2_fastqc.html

file:///C:/Users/pkiehl/Documents/Paenibacillus_Nanopore_fastqc.html

![Screenshot (5)](https://user-images.githubusercontent.com/116305887/204600864-f06b7506-b114-4e99-84cb-5de8820e2554.png)

![Screenshot (7)](https://user-images.githubusercontent.com/116305887/204601143-26b328d0-3105-43cd-8d07-2940964f6950.png)

![Screenshot (8)](https://user-images.githubusercontent.com/116305887/204601165-60756147-60db-40b6-af00-0df04a7a60b3.png)

![Screenshot (9)](https://user-images.githubusercontent.com/116305887/204601198-53b5e5e7-ca06-4ef6-b7f6-b30055b59772.png)

![Screenshot (10)](https://user-images.githubusercontent.com/116305887/204601215-5688c3b2-fd50-4075-94a5-ff910ff5b47f.png)

![Screenshot (11)](https://user-images.githubusercontent.com/116305887/204601233-ade19dce-69d5-478d-ad96-69f3d06f51df.png)

![Screenshot (12)](https://user-images.githubusercontent.com/116305887/204601249-9ccabee5-03ef-403c-9fd2-a5ab8bdb65f5.png)

![Screenshot (13)](https://user-images.githubusercontent.com/116305887/204601267-e212db36-8a4f-4762-8b59-dcd2fffa73fd.png)

![Screenshot (14)](https://user-images.githubusercontent.com/116305887/204601286-8641e13f-125f-44c2-bd1b-d33b530088ce.png)

## Step 4: Trim Adapters

The Illumina Universal Adapter sequence is AGATCGGAAGAG.

On Ubuntu: 

cutadapt \
   -a AGATCGGAAGAG \
   -A AGATCGGAAGAG \
   -q 30,30 \
   --minimum-length 80 \
   -o Paenibacillus_Illumina_R1_trimmed.fastq \
   -p Paenibacillus_Illumina_R2_trimmed.fastq \
   Paenibacillus_Illumina_R1.fastq \
   Paenibacillus_Illumina_R2.fastq \
   | tee cutadapt.log
 
Results: 

=== Summary ===

Total read pairs processed:            807,033
  Read 1 with adapter:                 302,507 (37.5%)
  Read 2 with adapter:                 288,766 (35.8%)

== Read fate breakdown ==
Pairs that were too short:              14,956 (1.9%)
Pairs written (passing filters):       792,077 (98.1%)

Total basepairs processed:   403,516,500 bp
  Read 1:   201,758,250 bp
  Read 2:   201,758,250 bp
Quality-trimmed:               9,926,093 bp (2.5%)
  Read 1:     1,945,257 bp
  Read 2:     7,980,836 bp
Total written (filtered):    371,924,944 bp (92.2%)
  Read 1:   186,688,646 bp
  Read 2:   185,236,298 bp

=== First read: Adapter 1 ===

Sequence: AGATCGGAAGAG; Type: regular 3'; Length: 12; Trimmed: 302507 times

Minimum overlap: 3
No. of allowed errors:
1-9 bp: 0; 10-12 bp: 1

Bases preceding removed adapters:
  A: 26.2%
  C: 20.1%
  G: 20.4%
  T: 33.2%
  none/other: 0.0%
  
=== Second read: Adapter 2 ===

Sequence: AGATCGGAAGAG; Type: regular 3'; Length: 12; Trimmed: 288766 times

Minimum overlap: 3
No. of allowed errors:
1-9 bp: 0; 10-12 bp: 1

Bases preceding removed adapters:
  A: 25.7%
  C: 20.0%
  G: 20.7%
  T: 33.6%
  none/other: 0.0%
 
## Step 5: Quality Check #2

On Ubuntu: 

fastqc Paenibacillus_Illumina_R1_trimmed.fastq

fastqc Paenibacillus_Illumina_R2_trimmed.fastq

On Terminal:

cd .\Documents\

sftp pkiehl@thoth01.cvmbs.colostate.edu

cd final_project_MIP280A4

get Paenibacillus_Illumina_R1_trimmed_fastqc.html

get Paenibacillus_Illumina_R2_trimmed_fastqc.html

## Step 6: Assembly

On Ubuntu:

spades.py   -o Paenibacillus_spades_assembly \
   --pe1-1 Paenibacillus_Illumina_R1_trimmed.fastq \
   --pe1-2  Paenibacillus_Illumina_R2_trimmed.fastq \
   --nanopore Paenibacillus_Nanopore.fastq \
   -m 24 -t 18
   
 cd Paenibacillus_spades_assembly/

seqtk seq -A contigs.fasta | head -2 > first_contig.fasta

The first contig is 1144848 bases long. 

On Terminal:

cd .\Documents\

sftp pkiehl@thoth01.cvmbs.colostate.edu

cd final_project_MIP280A4

cd Paenibacillus_spades_assembly

get first_contig.fasta

## Step 6: BLAST

First 1000 bases of first contig

![Screenshot (15)](https://user-images.githubusercontent.com/116305887/204674746-d5554458-ae9a-46ee-8324-7476411796d7.png)

First 2500 bases of first contig

![Screenshot (16)](https://user-images.githubusercontent.com/116305887/204674769-b3439638-142e-4de6-91d7-44be05f47ae7.png)

Middle bases from first contigs (lines 17015-17427 in Notepad++ (approximately bases 1,020,900 - 1,045,620))

![Screenshot (17)](https://user-images.githubusercontent.com/116305887/204676059-aed5529a-4aee-49c7-94f7-2b5f6218905c.png)

Contig #10

![Screenshot (18)](https://user-images.githubusercontent.com/116305887/204676092-6a87d752-50e3-4999-be40-1d032a0e7a08.png)

