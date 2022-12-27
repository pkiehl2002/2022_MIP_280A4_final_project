# MIP 280A4 Final Project 2022

This report documents the final project I did for Colorado State University's class MIP 280A4, Microbial Sequence Analysis, in the fall of 2022. I analyzed a <em>Paenibacillus</em> genome because this bacteria demonstrated unique morphological, biochemical, and antimicrobial properties but was unable to be identified using 16S sequencing. With Illumina and Nanopore full genome sequencing data, I trimmed, assembled, mapped, and annotated this genome. The workflow, results, packages, and figures of this project can be found in this file. These files are written in [Markdown format](https://www.markdownguide.org/basic-syntax/).  

## Step 1: Quality Testing
	
This genome was sequenced using both Illumina and Nanopore technologies. Before I used this information to assemble the genome, I analyzed the quality of the data. 
	
On Ubuntu: (THOTH server: thoth01.cvmbs.colostate.edu) /home/pkiehl

```
conda activate bio_tools
cd /home/data_for_classes/2022_MIP_280A4/final_project_datasets
cp Paenibacillus_Illumina_R1.fastq ~/final_project_MIP280A4
cp Paenibacillus_Illumina_R2.fastq ~/final_project_MIP280A4/
cp Paenibacillus_Nanopore.fastq.gz ~/final_project_MIP280A4/
cd ~/final_project_MIP280A4/
fastqc Paenibacillus_Illumina_R1.fastq
fastqc Paenibacillus_Illumina_R2.fastq
fastqc Paenibacillus_Nanopore.fastq.gz
```

On Terminal: /home

```
cd .\Documents\
```
sftp into server thoth01.cvmbs.colostate.edu
```
cd final_project_MIP280A4
get Paenibacillus_Illumina_R1_fastqc.html
get Paenibacillus_Illumina_R2_fastqc.html
get Paenibacillus_Nanopore_fastqc.html
```

### Pre-Trimming FASTQC Results

Illumina R1 Quality

![Screenshot 2022-12-03 at 3 46 08 PM](https://user-images.githubusercontent.com/116305887/205465209-b6255dcb-affb-4264-99ec-465605a0fe2d.jpg)

Illumina R2 Quality

![Screenshot 2022-12-03 at 3 46 31 PM](https://user-images.githubusercontent.com/116305887/205465233-2810a8e3-e599-4fc5-b098-93cbaf83c2a7.jpg)

Nanopore Quality

![Screenshot 2022-12-05 at 9 16 43 PM](https://user-images.githubusercontent.com/116305887/205812473-e64bd71d-0c19-4fa3-904c-edfa4d0928bd.jpg)

Illumina R1 Adapter Content Before Trimming

![Screenshot 2022-12-03 at 3 47 47 PM](https://user-images.githubusercontent.com/116305887/205465284-746a7851-ce7c-478a-8e0c-f77e7877b22b.jpg)

Illumina R2 Adapter Content Before Trimming

![Screenshot 2022-12-03 at 3 48 06 PM](https://user-images.githubusercontent.com/116305887/205465295-f4bfc274-0181-499b-afa8-f3a2d25ac747.jpg)

As expected, the Nanopore quality was low, so it will not be trimmed. The Illumina Universal Adapter sequence was found in the Illumina reads. The next step is to remove those adapters. 

## Step 2: Trim Adapters

The Illumina Universal Adapter sequence is AGATCGGAAGAG, which will be used in cutadapt so they are properly removed from the data. 

On Ubuntu: (THOTH server: thoth01.cvmbs.colostate.edu) /home/pkiehl/final_project_MIP280A4
```
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
```
### Trimming Results: 
 ```
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
```
 
## Step 3: Quality Check #2

To make sure trimming worked successfully, a second FASTQC report was done to test the read quality. 

On Ubuntu: (THOTH server: thoth01.cvmbs.colostate.edu) /home/pkiehl/final_project_MIP280A4

```
fastqc Paenibacillus_Illumina_R1_trimmed.fastq

fastqc Paenibacillus_Illumina_R2_trimmed.fastq
```

On Terminal: /home

```
cd .\Documents\
```

sftp into server thoth01.cvmbs.colostate.edu
```
cd final_project_MIP280A4

get Paenibacillus_Illumina_R1_trimmed_fastqc.html

get Paenibacillus_Illumina_R2_trimmed_fastqc.html
```

### Post-Trimming FASTQC Reports

Illumina R1 Adapter Content

![Screenshot 2022-12-03 at 4 10 22 PM](https://user-images.githubusercontent.com/116305887/205465966-7e8a4cd3-2470-41de-afa1-774e92fce90e.jpg)

Illumina R2 Adapter Content

![Screenshot 2022-12-03 at 4 10 47 PM](https://user-images.githubusercontent.com/116305887/205465978-187eb06f-0ec0-45df-ad66-ff2cd49c4ae0.jpg)
Trimming the adapters successfully removed the adapters, so this data can be used for the genome assembly 


## Step 4: Assembly

On Ubuntu: (THOTH server: thoth01.cvmbs.colostate.edu) /home/pkiehl/final_project_MIP280A4

```
spades.py   -o Paenibacillus_spades_assembly_2 \
   --pe1-1 Paenibacillus_Illumina_R1_trimmed.fastq \
   --pe1-2  Paenibacillus_Illumina_R2_trimmed.fastq \
   --nanopore Paenibacillus_Nanopore.fastq.gz \
   -m 24 -t 18
   
cd Paenibacillus_spades_assembly_2/

seqtk seq -A scaffolds.fasta | head -2 > first_scaffold.fasta

grep NODE scaffolds.fasta

grep NODE contigs.fasta
```

The first scaffold is 5,045,338 bases long. 

On Terminal: /home

```
cd .\Documents\
```
sftp into server thoth01.cvmbs.colostate.edu
```
cd final_project_MIP280A4
cd Paenibacillus_spades_assembly_2
get first_scaffold.fasta
```

## Step 5: Assembly Statistics

On Ubuntu: (THOTH server: thoth01.cvmbs.colostate.edu) /home/pkiehl/final_project_MIP280A4/Paenibacillus_spades_assembly_2

```
conda install -c bioconda quast
quast.py scaffolds.fasta -o scaffolds_stats
head scaffolds_stats
```

The N50 is 5,045,338 and the L50 is 1. More calculations can be found below. 

### Assembly Statistics

![Screenshot 2022-12-01 at 11 21 21 AM](https://user-images.githubusercontent.com/116305887/205130601-008d71a3-a453-43ee-bd61-136601f08b88.jpg)

## Step 6: BLAST

Search BLAST database with default settings: https://blast.ncbi.nlm.nih.gov/Blast.cgi?PROGRAM=blastn&PAGE_TYPE=BlastSearch&LINK_LOC=blasthome

Import first_scaffold.fasta into Notepad++ and copy sequences into BLAST. Repeat for all scaffolds. 

Scaffold #1 from Notepad++ middle bases (lines 3600-4018)

Top result of <em>Paenibacillus bovis</em> with 76.98% match and an E-value of 0.  

![Screenshot 2022-12-03 at 4 37 17 PM](https://user-images.githubusercontent.com/116305887/205466736-58c62a57-39aa-4f49-ab2f-b251d458de86.jpg)

<em>Paenibacillus bovis</em> is consistently the top hit across scaffolds, though there are mixed results. This implies that <em>Paenibacillus bovis</em> is the most closely related bacteria, but it is not a strong relation. <em>Mastacembelus armatus</em> was a top results twice in smaller scaffolds. The BLAST results of multiple searches can be found below. 

### BLAST Results

Scaffold #1 (lines 55515-55939)

![Screenshot 2022-12-27 at 12 18 20 PM](https://user-images.githubusercontent.com/116305887/209712819-75c88d38-d252-47a5-969c-bd7e099f3eca.jpg)

Scaffold #1 (lines 54815-55626)

![Screenshot 2022-12-27 at 12 20 06 PM](https://user-images.githubusercontent.com/116305887/209712922-f8949810-acca-4fe1-8865-e633b1f5bef1.jpg)

Scaffold #1 (lines 53693-54126)

![Screenshot 2022-12-27 at 12 20 49 PM](https://user-images.githubusercontent.com/116305887/209712978-22b0763f-8ef4-405d-9fbe-ea2c41f8ae4c.jpg)

Scaffold #1 (lines 51400-51814)

![Screenshot 2022-12-27 at 12 22 05 PM](https://user-images.githubusercontent.com/116305887/209713081-e5b2b74d-083e-4fe2-adaa-a41f8025819a.jpg)

DNA polymerase I 

![Screenshot 2022-12-27 at 12 22 33 PM](https://user-images.githubusercontent.com/116305887/209713117-63a6beec-b607-4d34-8f03-06125ce73908.jpg)

Scaffold #2

![Screenshot 2022-12-03 at 4 45 20 PM](https://user-images.githubusercontent.com/116305887/205467047-0e9e5f1a-23ea-4728-b15c-2d39e6b52f63.jpg)

Scaffold #3

![Screenshot 2022-12-03 at 4 45 54 PM](https://user-images.githubusercontent.com/116305887/205467063-36859e9e-69a3-4430-bb60-63b40f09ec30.jpg)

Scaffold #4

![Screenshot 2022-12-03 at 4 46 17 PM](https://user-images.githubusercontent.com/116305887/205467070-f908b057-b921-4973-bf2d-06d318627f2f.jpg)

Scaffold #5

![Screenshot 2022-12-03 at 4 46 36 PM](https://user-images.githubusercontent.com/116305887/205467075-a85ea45a-d010-4f05-96e7-fd65c15c030e.jpg)

Scaffold #6

![Screenshot 2022-12-03 at 4 46 56 PM](https://user-images.githubusercontent.com/116305887/205467088-a2e9621d-2267-401e-9654-0153b0df1757.jpg)

Scaffold #7

![Screenshot 2022-12-03 at 4 49 22 PM](https://user-images.githubusercontent.com/116305887/205467127-17e253e9-a239-45e6-9fea-bc588ad7e896.jpg)

Scaffold #8

![Screenshot 2022-12-03 at 4 50 38 PM](https://user-images.githubusercontent.com/116305887/205467146-7d1d2a87-0221-4841-96b6-6ce8f8db285a.jpg)

Scaffold #9

![Screenshot 2022-12-03 at 4 51 13 PM](https://user-images.githubusercontent.com/116305887/205467157-01a90dd6-b89c-4612-b049-c03b09d3bcdd.jpg)

Scaffold #10

![Screenshot 2022-12-03 at 4 51 50 PM](https://user-images.githubusercontent.com/116305887/205467170-95f8b7e2-1e3e-4eb7-9221-23a579ffd4a2.jpg)

## Step 7: Alignment

The assembled genome was aligned against the reference sequence of <em>Paenibacillus bovis</em> using Geneious' full genome alignment tools. 

Import first_scaffold.fasta into Geneious

Align first scaffold from assembly with CP013023 <em>Paenibacillus bovis</em> reference sequence. 

LASTZ alignment has a pairwise identity of 75.6%, which is approximately the same as the BLAST results. Geneious diagrams of the alignments can be found below.

### Alignment Diagrams

Mauve Diagram

![Screenshot (42)](https://user-images.githubusercontent.com/116305887/205790269-7b3e3243-d42c-4126-a9ec-df7eeb4d651b.png)

LASTZ alignment

![Screenshot (39)](https://user-images.githubusercontent.com/116305887/205471678-eab0b57d-c181-4c16-97ba-04e5f657b821.png)
The red diagonal line shows an inversion in the bacterial genome compared to the reference sequence.

## Step 8: Mapping Illumina Reads to Scaffolds and Calculating Coverage Depth

The Illumina reads were mapped to the largest scaffold to analyze the alignment and coverage depth of the reads. 

In Ubunutu: (THOTH server: thoth01.cvmbs.colostate.edu) /home/pkiehl/final_project_MIP280A4/Paenibacillus_spades_assembly

```
bowtie2-build scaffolds.fasta penny_scaffolds_index 
cd ..
mv Paenibacillus_Illumina_R1_trimmed.fastq Paenibacillus_spades_assembly_2/
mv Paenibacillus_Illumina_R2_trimmed.fastq Paenibacillus_spades_assembly_2/
cd Paenibacillus_spades_assembly_2/
bowtie2 -x penny_scaffolds_index \
   -1 Paenibacillus_Illumina_R1_trimmed.fastq \
   -2 Paenibacillus_Illumina_R2_trimmed.fastq \
   --no-unal \
   --threads 8 \
   -S Paenibacillus_Illumina_mapped_to_penny_scaffolds.sam
```
There was a 99.51% alignment. 
```
samtools view -b Paenibacillus_Illumina_mapped_to_penny_scaffolds.sam > Paenibacillus_Illumina_mapped_to_penny_scaffolds.bam 
samtools sort -T tmp -O 'bam' Paenibacillus_Illumina_mapped_to_penny_scaffolds.bam  > Paenibacillus_Illumina_mapped_to_penny.sorted.bam   
samtools coverage Paenibacillus_Illumina_mapped_to_penny.sorted.bam 
```

The average coverage depth was 73.322X. The full analysis can be seen below. 

### Mapping Reads and Coverage Depth Calculations

Illumina reads mapped in Geneious

![Screenshot (36)](https://user-images.githubusercontent.com/116305887/205470784-33addd2d-95b2-4510-bbf4-75a73a1f7116.png)

![Screenshot (37)](https://user-images.githubusercontent.com/116305887/205470789-7f83c6c3-08f4-46fc-b521-c93394208e0b.png)

Nanopore reads mapped in Geneious

![Screenshot (34)](https://user-images.githubusercontent.com/116305887/205470795-168c76f0-c476-464e-8f7a-4a8200171ad6.png)

![Screenshot (35)](https://user-images.githubusercontent.com/116305887/205470799-c4aea8bd-2335-46e0-8246-3ac0fa7d2f0f.png)

Illumina reads coverage calculations

![Screenshot 2022-12-05 at 9 11 05 PM](https://user-images.githubusercontent.com/116305887/205811582-614cc76f-0497-4408-803f-88500e0538ce.jpg)

Nanopore reads coverage calculations

![Screenshot 2022-12-01 at 6 37 21 PM](https://user-images.githubusercontent.com/116305887/205195050-dc56681a-2b58-41a1-ac37-a7e47429ca29.jpg)

## Step 9: Mapping Nanopore Reads to Scaffolds and Calculating Coverage Depth

In Ubuntu: (THOTH) /home/pkiehl/final_project_MIP280A4

```
conda install -c bioconda minimap2
minimap2 -a -x map-ont Paenibacillus_spades_assembly_2/scaffolds.fasta Paenibacillus_Nanopore.fastq.gz > approx-mapping_new_nanopore_to_scaffold.sam
```

```
samtools view -b approx-mapping_new_nanopore_to_scaffold.sam > nanopore_mapped_to_scaffolds.bam
samtools sort -T tmp -O 'bam' nanopore_mapped_to_scaffolds.bam > nanopore_mapped_to_scaffolds_sorted.bam
samtools coverage nanopore_mapped_to_scaffolds_sorted.bam  
```
In Termainal:

sftp into server
```
cd final_project_MIP280A4
cd Paenibacillus_spades_assembly_2
get approx-mapping_new_nanopore_to_scaffold.sam
```

In Geneious:

import approx-mapping_new_nanopore_to_scaffold.sam
Align this file to the first_scaffold

The average coverage depth was 13.1892. The full analysis can be seen in additional_information.md. 

## Step 10: Annotation

In Ubuntu: (THOTH) /home/pkiehl/final_project_MIP280A4/Paenibacillus_spades_assembly_2

```
conda install -c conda-forge -c bioconda bakta
bakta --db /home/data_for_classes/bakta_database/db scaffolds.fasta
amrfinder_update --database /home/data_for_classes/bakta_database/db/amrfinderplus-db 
```

In Geneious: 
- Import annotation 
- Export annotations table 
- Import table into text editor
- sftp into server
- put file in server
- wc -l [file]

4,537 lines -1 = 4,536 genes were found in the annotation. The bakta output and annotations in Geneious can be found in additional_information.md.

 
## Step 11: Nanoplot

In Ubuntu: (THOTH) /home/pkiehl/final_project_MIP280A4

```
conda install -c bioconda nanoplot
NanoPlot -t 8 --fastq Paenibacillus_Nanopore.fastq.gz --plots
```

## Step 12: Assess Annotation

In Unbuntu: (THOTH) /home/pkiehl/final_project_MIP280A4/spades_assembly_2

```
singularity shell /home/data_for_classes/singularity_images/busco-5.4.3--pyhdfd78af_0
busco -i first_scaffold.fasta -o busco_output_first_scaffold -m genome
```

## Step 13: DNA Polymerase I Alignment

Copy DNA polymerase I reference sequence protein sequence from https://www.ncbi.nlm.nih.gov/nucleotide/CP013023.1?report=genbank&log$=nuclalign&blast_rank=1&RID=SZ0D63T2016 into Notepadd++. 

Copy DNA polymerase I protein sequence from aligned genome in Geneious and paste into Notepadd++.

Import both files into Geneious and make pairwise alignment using Needleman-Wunsch algorithim. 

Copy DNA polymerase I reference sequence DNA sequence from CP013023 into Notepad++. 

Copy DNA polymerase I DNA sequence from aligned genome in Geneious and paste into Notepad++.

Import both files into Geneious and make pairwise alignment using Needleman-Wunsch algorithim. 

81.2% pairwise identity and 90.5% pairwise positive for protein sequence. 

73.9% pairwise identity for DNA sequence.
