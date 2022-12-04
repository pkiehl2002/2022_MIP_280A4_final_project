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

## Step 5: BLAST

Scaffold #1 from Notepad++ middle bases (lines 3600-4018)

Top result of <em>Paenibacillus bovis</em> with 76.98% match and an E-value of 0.  

![Screenshot 2022-12-03 at 4 37 17 PM](https://user-images.githubusercontent.com/116305887/205466736-58c62a57-39aa-4f49-ab2f-b251d458de86.jpg)

The BLAST results of the remaining scaffolds can be found in additional_information.md. 

## Step 6: Mapping Illumina Reads to Scaffolds

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

## Step 7: Mapping Nanopore Reads to Scaffolds

```
conda install -c bioconda minimap2
minimap2 -a -x map-ont Paenibacillus_spades_assembly_2/scaffolds.fasta Paenibacillus_Nanopore.fastq.gz > approx-mapping_new_nanopore_to_scaffold.sam
```

https://www.sciencedirect.com/science/article/abs/pii/S014486171630114X


 
 ## Coverage Depth
 
 samtools view -b Paenibacillus_Illumina_mapped_to_penny_scaffolds.sam > Paenibacillus_Illumina_mapped_to_penny_scaffolds.bam
 
 samtools sort -T tmp -O 'bam' Paenibacillus_Illumina_mapped_to_penny_scaffolds.bam  > Paenibacillus_Illumina_mapped_to_penny.sorted.bam
   
 samtools depth Paenibacillus_Illumina_mapped_to_penny.sorted.bam > Paenibacillus_Illumina_mapped_to_penny_sorted_depth
 
## Other
 
conda install -c conda-forge -c bioconda bakta
 
bakta --db ADD PATHWAY first_scaffold.fasta
 
conda install -c bioconda quast

quast.py scaffolds.fasta -o scaffolds_stats

![Screenshot 2022-12-01 at 11 21 21 AM](https://user-images.githubusercontent.com/116305887/205130601-008d71a3-a453-43ee-bd61-136601f08b88.jpg)


TODO:
- annotate w/ Batka
conda install -c conda-forge -c bioconda bakta
bakta --db /home/data_for_classes/bakta_database/db scaffolds.fasta
amrfinder_update --database /home/data_for_classes/bakta_database/db/amrfinderplus-db 

parse genome sequences...
	imported: 21
	filtered & revised: 21
	contigs: 21

start annotation...
predict tRNAs...
	found: 79
predict tmRNAs...
	found: 1
predict rRNAs...
	found: 25
predict ncRNAs...
	found: 5
predict ncRNA regions...
	found: 48
predict CRISPR arrays...
	found: 2
predict & annotate CDSs...
	predicted: 4385 
	discarded spurious: 0
	revised translational exceptions: 0
	detected IPSs: 1933
	found PSCs: 2179
	found PSCCs: 163
	lookup annotations...
	conduct expert systems...
		amrfinder: 1
		protein sequences: 3
	combine annotations and mark hypotheticals...
	detect pseudogenes...
		pseudogene candidates: 14
		found pseudogenes: 10
analyze hypothetical proteins: 618
	detected Pfam hits: 72 
	calculated proteins statistics
	revise special cases...
extract sORF...
	potential: 82636
	discarded due to overlaps: 68156
	discarded spurious: 0
	detected IPSs: 1
	found PSCs: 0
	lookup annotations...
	filter and combine annotations...
	filtered sORFs: 1
detect gaps...
	found: 9
detect oriCs/oriVs...
	found: 0
detect oriTs...
	found: 0
apply feature overlap filters...
select features and create locus tags...
selected: 4546

genome statistics:
	Genome size: 5,050,086 bp
	Contigs/replicons: 21
	GC: 39.4 %
	N50: 5,045,338
	N ratio: 0.0 %
	coding density: 86.8 %

annotation summary:
	tRNAs: 79
	tmRNAs: 1
	rRNAs: 25
	ncRNAs: 5
	ncRNA regions: 46
	CRISPR arrays: 2
	CDSs: 4378
	  hypotheticals: 611
	  pseudogenes: 10
	  signal peptides: 0
	sORFs: 1
	gaps: 9
	oriCs/oriVs: 0
	oriTs: 0

export annotation results to: /home/pkiehl/final_project_MIP280A4/Paenibacillus_spades_assembly_2
	human readable TSV...
	GFF3...
	INSDC GenBank & EMBL...
	genome sequences...
	feature nucleotide sequences...
	translated CDS sequences...
	circular genome plot...
/home/pkiehl/miniconda3/envs/bio_tools/lib/python3.9/site-packages/Bio/SeqUtils/__init__.py:144: BiopythonDeprecationWarning: GC is deprecated; please use gc_fraction instead.
  warnings.warn(
	hypothetical TSV...
	translated hypothetical CDS sequences...
	machine readable JSON...
	genome and annotation summary...
- map nanopore reads to scaffolds w/ minimap2

- cool QC nanopore visualization
conda install -c bioconda nanoplot
NanoPlot -t 8 --fastq Paenibacillus_Nanopore.fastq.gz --plots
file:///Users/pariskiehl/Desktop/CSUSemester3/NanoPlot-report.html
- determine average coverage depth from Paenibacillus_Illumina_mapped_to_penny_sorted_depth

samtools coverage Paenibacillus_Illumina_mapped_to_penny.sorted.bam

![Screenshot 2022-12-01 at 6 36 56 PM](https://user-images.githubusercontent.com/116305887/205194996-009587b1-dc2d-4e76-b8d9-ac793793b21b.jpg)

samtools view -b approx-mapping_new_nanopore_to_scaffold.sam > nanopore_mapped_to_scaffolds.bam

samtools sort -T tmp -O 'bam' nanopore_mapped_to_scaffolds.bam > nanopore_mapped_to_scaffolds_sorted.bam

samtools coverage nanopore_mapped_to_scaffolds_sorted.bam  

![Screenshot 2022-12-01 at 6 37 21 PM](https://user-images.githubusercontent.com/116305887/205195050-dc56681a-2b58-41a1-ac37-a7e47429ca29.jpg)

