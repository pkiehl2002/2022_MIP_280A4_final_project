# MIP 280A4 Final Project 2022

This report documents the final project I did for MIP 280A4, Microbial Sequence Analysis, in the fall of 2022. I analyzed a <em>Paenibacillus</em> genome with antimicrobial properties that was isolated from soil. I trimmed, assembled, mapped, and annotated this genome. Packages, figures, and extra data can be found in additional_information.md. This file is written in [Markdown format](https://www.markdownguide.org/basic-syntax/).  

## Step 1: Quality Testing
	
This genome was sequenced using both Illumina and Nanopore technologies. Before I used this information to assemble the genome, I analyzed the quality of the data. 
	
On Ubuntu:

```
ssh pkiehl@thoth01.cvmbs.colostate.edu
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

On Terminal:

```
cd .\Documents\
sftp pkiehl@thoth01.cvmbs.colostate.edu
cd final_project_MIP280A4
get Paenibacillus_Illumina_R1_fastqc.html
get Paenibacillus_Illumina_R2_fastqc.html
get Paenibacillus_Nanopore_fastqc.html
```

Specific data from FASTQC report can be found in additional_information.md. As expected, the nanopore quality was low. Also, the Illumina Universal Adapter sequence was found in the Illumina reads. The next step is to remove those adapters. 

## Step 2: Trim Adapters

The Illumina Universal Adapter sequence is AGATCGGAAGAG.

On Ubuntu: 
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

The results from the individual reads can be found in additional_information.md.
 
## Step 3: Quality Check #2

On Ubuntu: 

```
fastqc Paenibacillus_Illumina_R1_trimmed.fastq

fastqc Paenibacillus_Illumina_R2_trimmed.fastq
```

On Terminal:

```
cd .\Documents\

sftp pkiehl@thoth01.cvmbs.colostate.edu

cd final_project_MIP280A4

get Paenibacillus_Illumina_R1_trimmed_fastqc.html

get Paenibacillus_Illumina_R2_trimmed_fastqc.html
```
Trimming the adapters successfully removed the adapters. The specific FASTQC reports can be found in additional_information.md.

## Step 4: Assembly

On Ubuntu:

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

On Terminal:

```
cd .\Documents\
sftp pkiehl@thoth01.cvmbs.colostate.edu
cd final_project_MIP280A4
cd Paenibacillus_spades_assembly_2
get first_scaffold.fasta
```

## Step 5: Assembly Statistics

```
conda install -c bioconda quast
quast.py scaffolds.fasta -o scaffolds_stats
head scaffolds_stats
```

The N50 is 5,045,338 and the L50 is 1. More calculations can be found in additional_information.md.

## Step 6: BLAST

Scaffold #1 from Notepad++ middle bases (lines 3600-4018)

Top result of <em>Paenibacillus bovis</em> with 76.98% match and an E-value of 0.  

![Screenshot 2022-12-03 at 4 37 17 PM](https://user-images.githubusercontent.com/116305887/205466736-58c62a57-39aa-4f49-ab2f-b251d458de86.jpg)

<em>Paenibacillus bovis</em> is consistently the top result across scaffolds. <em>Mastacembelus armatus</em> appeared twice in the results. The BLAST results of the remaining scaffolds can be found in additional_information.md. 

## Step 7: Alignment

Align first five scaffolds from assembly with CP013023 <em>Paenibacillus bovis</em> reference sequence. 

LASTZ alignment has a pairwise identity of 75.6%. Geneious diagrams of the alignments can be found in additional_information.md.

## Step 8: Mapping Illumina Reads to Scaffolds and Calculating Coverage Depth

```
bowtie2-build scaffolds.fasta penny_scaffolds_index 
cd ..
mv Paenibacillus_Illumina_R1_trimmed.fastq Paenibacillus_spades_assembly/
mv Paenibacillus_Illumina_R2_trimmed.fastq Paenibacillus_spades_assembly/
cd Paenibacillus_spades_assembly/
bowtie2 -x penny_scaffolds_index \
   -1 Paenibacillus_Illumina_R1_trimmed.fastq \
   -2 Paenibacillus_Illumina_R2_trimmed.fastq \
   --no-unal \
   --threads 8 \
   -S Paenibacillus_Illumina_mapped_to_penny_scaffolds.sam
```
```
samtools view -b Paenibacillus_Illumina_mapped_to_penny_scaffolds.sam > Paenibacillus_Illumina_mapped_to_penny_scaffolds.bam 
samtools sort -T tmp -O 'bam' Paenibacillus_Illumina_mapped_to_penny_scaffolds.bam  > Paenibacillus_Illumina_mapped_to_penny.sorted.bam   
samtools depth Paenibacillus_Illumina_mapped_to_penny.sorted.bam > Paenibacillus_Illumina_mapped_to_penny_sorted_depth
```

The average coverage depth was 74.3249. The full analysis can be seen in additional_information.md. 

## Step 9: Mapping Nanopore Reads to Scaffolds and Calculating Coverage Depth

```
conda install -c bioconda minimap2
minimap2 -a -x map-ont Paenibacillus_spades_assembly_2/scaffolds.fasta Paenibacillus_Nanopore.fastq.gz > approx-mapping_new_nanopore_to_scaffold.sam
```

```
samtools view -b approx-mapping_new_nanopore_to_scaffold.sam > nanopore_mapped_to_scaffolds.bam
samtools sort -T tmp -O 'bam' nanopore_mapped_to_scaffolds.bam > nanopore_mapped_to_scaffolds_sorted.bam
samtools coverage nanopore_mapped_to_scaffolds_sorted.bam  
```

The average coverage depth was 13.1892. Combined with the Illumina data, the total average coverage is 87.5141. The full analysis can be seen in additional_information.md. 

## Step 10: Annotation

```
conda install -c conda-forge -c bioconda bakta
bakta --db /home/data_for_classes/bakta_database/db scaffolds.fasta
amrfinder_update --database /home/data_for_classes/bakta_database/db/amrfinderplus-db 
```
4,539 genes were found in the annotation. The bakta output and annotations in Geneious can be found in additional_information.md.

 
## Step 11: Nanoplot

```
conda install -c bioconda nanoplot
NanoPlot -t 8 --fastq Paenibacillus_Nanopore.fastq.gz --plots
```

