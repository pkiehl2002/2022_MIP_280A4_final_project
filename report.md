# MIP 280A4 Final Project 2022

This report documents the final project I did for MIP 280A4, Microbial Sequence Analysis, in the fall of 2022. I analyzed a <em>Paenibacillus</em> genome with antimicrobial properties that was isolated from soil. I trimmed, assembled, mapped, and annotated this genome. Packages, figures, and extra data can be found in additional_information.md. This file is written in [Markdown format](https://www.markdownguide.org/basic-syntax/).  

## Step 1: Quality Test
	
This genome was sequenced using both Illumina and Nanopore technologies. Before I used this information to assemble the genome, I analyzed the quality of the data. 
	
On Ubuntu:

```
ssh pkiehl@thoth01.cvmbs.colostate.edu
conda activate bio_tools
cd /home/data_for_classes/2022_MIP_280A4/final_project_datasets
cp Paenibacillus_Illumina_R1.fastq ~/final_project_MIP280A4
cp Paenibacillus_Illumina_R2.fastq ~/final_project_MIP280A4/
cp Paenibacillus_Nanopore.fastq ~/final_project_MIP280A4/
cd ~/final_project_MIP280A4/
fastqc Paenibacillus_Illumina_R1.fastq
fastqc Paenibacillus_Illumina_R2.fastq
fastqc Paenibacillus_Nanopore.fastq
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

try blastn too!

First 1000 bases of first contig

![Screenshot (15)](https://user-images.githubusercontent.com/116305887/204674746-d5554458-ae9a-46ee-8324-7476411796d7.png)

First 2500 bases of first contig

![Screenshot (16)](https://user-images.githubusercontent.com/116305887/204674769-b3439638-142e-4de6-91d7-44be05f47ae7.png)

Middle bases from first contigs (lines 17015-17427 in Notepad++ (approximately bases 1,020,840 - 1,045,560))

![Screenshot (17)](https://user-images.githubusercontent.com/116305887/204676059-aed5529a-4aee-49c7-94f7-2b5f6218905c.png)

Middle bases from first contig (lines 7227 - 7651 (approximately bases 433,560 - 459,000)

![Screenshot (19)](https://user-images.githubusercontent.com/116305887/204676463-6ff340c9-d7cf-4cee-b750-4a418c7eeaf8.png)

Middle bases from first contigs (lines 12000 - 12413 (approximately bases 719,940 - 744,720))

![Screenshot (20)](https://user-images.githubusercontent.com/116305887/204677030-4872f9b2-58fe-4dfc-b506-45705abcae90.png)

Last bases from first contigs (lines 18671 - 19082 (approximately bases 1,120,200 - 1,144,848))

![Screenshot (21)](https://user-images.githubusercontent.com/116305887/204678163-c9cfeea8-eb4f-4bb3-bb97-0e245921b2f4.png)

Contig #2

Middle bases (lines 19213-19638)

![Screenshot (22)](https://user-images.githubusercontent.com/116305887/204949788-d1d6ba61-3f7c-4511-81a3-50750ba1dd98.png)
 
Contig #3 

Middle bases (lines 30590-31010)

![Screenshot (23)](https://user-images.githubusercontent.com/116305887/204950331-38f9b22d-7d28-4ebc-906a-486c571f3f37.png)

Contig #4

Middle bases (lines 41530-41945)

![Screenshot (24)](https://user-images.githubusercontent.com/116305887/204950684-8e5f6011-b762-4ea0-846c-9d7076362cdc.png)

Contig #5

Middle bases (lines 52668-53078)

![Screenshot (25)](https://user-images.githubusercontent.com/116305887/204951120-94b2517d-932c-4c3c-8e86-39794616b0a9.png)

Contigs #6

Middle bases (lines 6337-63750)

![Screenshot (26)](https://user-images.githubusercontent.com/116305887/204951215-90c3bdbd-6563-4d42-bb58-fcb2138f6481.png)

Contig #7

Middle bases (lines 72480-72900) 

![Screenshot (27)](https://user-images.githubusercontent.com/116305887/204951728-51a908ec-4c1f-4a14-95c2-2ab5d640c349.png)

Contig #10

![Screenshot (18)](https://user-images.githubusercontent.com/116305887/204676092-6a87d752-50e3-4999-be40-1d032a0e7a08.png)

Scaffold #1

Middle bases (lines 3600-4018)

![Screenshot (28)](https://user-images.githubusercontent.com/116305887/204953497-dcd4bc81-3146-4f14-bdd5-3cf8c70fda8a.png)

## Step ? Mapping Reads to Scaffolds

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

https://www.sciencedirect.com/science/article/abs/pii/S014486171630114X





## Spades Assembly w/ Updated Nanopore
spades.py   -o Paenibacillus_spades_assembly_2 \
   --pe1-1 Paenibacillus_Illumina_R1_trimmed.fastq \
   --pe1-2  Paenibacillus_Illumina_R2_trimmed.fastq \
   --nanopore Paenibacillus_Nanopore.fastq.gz \
   -m 24 -t 18
   
 Longest scaffold is 5045338 bases long.
 
 ## Coverage Depth
 
 samtools view -b Paenibacillus_Illumina_mapped_to_penny_scaffolds.sam > Paenibacillus_Illumina_mapped_to_penny_scaffolds.bam
 
 samtools sort -T tmp -O 'bam' Paenibacillus_Illumina_mapped_to_penny_scaffolds.bam  > Paenibacillus_Illumina_mapped_to_penny.sorted.bam
   
 samtools depth Paenibacillus_Illumina_mapped_to_penny.sorted.bam > Paenibacillus_Illumina_mapped_to_penny_sorted_depth
 
## Blast remaining contigs in better spades assembly

Contig #2 

![Screenshot (29)](https://user-images.githubusercontent.com/116305887/205126003-86f98896-4cac-4b09-a85b-bed12a1f926e.png)

Contig #3

![Screenshot (30)](https://user-images.githubusercontent.com/116305887/205126964-73df507f-8f7d-45d1-942b-5020b6caa7b8.png)

Contig #4 

![Screenshot (31)](https://user-images.githubusercontent.com/116305887/205127483-d259090e-1c65-424b-bc2a-1b7c2849c176.png)

Contig #5

![Screenshot (32)](https://user-images.githubusercontent.com/116305887/205127700-25a312f7-ba84-4ac2-9d17-100ab117fe49.png)

Contig #6

![Screenshot (33)](https://user-images.githubusercontent.com/116305887/205127891-8c1d1835-a9bb-4e61-a020-85efd3874778.png)
 
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
conda install -c bioconda minimap2
minimap2 -a -x map-ont Paenibacillus_spades_assembly_2/scaffolds.fasta Paenibacillus_Nanopore.fastq.gz > approx-mapping_new_nanopore_to_scaffold.sam
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

