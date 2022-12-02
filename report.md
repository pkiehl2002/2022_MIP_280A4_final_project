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

Conda environment bio_tools
conda list
# packages in environment at /home/pkiehl/miniconda3/envs/bio_tools:
#
# Name                    Version                   Build  Channel
_libgcc_mutex             0.1                 conda_forge    conda-forge
_openmp_mutex             4.5                       2_gnu    conda-forge
_r-mutex                  1.0.1               anacondar_1    conda-forge
_sysroot_linux-64_curr_repodata_hack 3                   h5bd9786_13    conda-forge
binutils_impl_linux-64    2.39                 h6ceecb4_0    conda-forge
binutils_linux-64         2.39                h5fc0e48_11    conda-forge
blast                     2.12.0               hf3cf87c_4    bioconda
boost-cpp                 1.74.0               h6cacc03_7    conda-forge
bowtie2                   2.4.5            py39h3321a2d_4    bioconda
brotli                    1.0.9                h166bdaf_7    conda-forge
brotli-bin                1.0.9                h166bdaf_7    conda-forge
brotlipy                  0.7.0           py39hb9d737c_1004    conda-forge
bwa                       0.7.17               h7132678_9    bioconda
bwidget                   1.9.14               ha770c72_1    conda-forge
bzip2                     1.0.8                h7f98852_4    conda-forge
c-ares                    1.18.1               h7f98852_0    conda-forge
ca-certificates           2022.9.24            ha878542_0    conda-forge
cairo                     1.16.0            ha12eb4b_1010    conda-forge
cd-hit                    4.8.1                h5b5514e_7    bioconda
cd-hit-auxtools           4.8.1                h9f5acd7_2    bioconda
certifi                   2022.9.24          pyhd8ed1ab_0    conda-forge
cffi                      1.15.1           py39he91dace_1    conda-forge
charset-normalizer        2.1.1              pyhd8ed1ab_0    conda-forge
click                     8.1.3            py39hf3d152e_0    conda-forge
coloredlogs               15.0.1             pyhd8ed1ab_3    conda-forge
colormath                 3.0.0                      py_2    conda-forge
commonmark                0.9.1                      py_0    conda-forge
contourpy                 1.0.5            py39hf939315_0    conda-forge
coreutils                 9.1                  h166bdaf_0    conda-forge
cryptography              38.0.2           py39hd97740a_1    conda-forge
curl                      7.85.0               h7bff187_0    conda-forge
cutadapt                  3.5              py39hbf8eff0_1    bioconda
cycler                    0.11.0             pyhd8ed1ab_0    conda-forge
dataclasses               0.8                pyhc8e2a94_3    conda-forge
diamond                   2.0.15               hb97b32f_1    bioconda
dnaio                     0.9.1            py39hbf8eff0_1    bioconda
entrez-direct             16.2                 he881be0_1    bioconda
expat                     2.4.9                h27087fc_0    conda-forge
fastp                     0.23.2               hb7a2d85_2    bioconda
fastqc                    0.11.9               hdfd78af_1    bioconda
font-ttf-dejavu-sans-mono 2.37                 hab24e00_0    conda-forge
font-ttf-inconsolata      3.000                h77eed37_0    conda-forge
font-ttf-source-code-pro  2.038                h77eed37_0    conda-forge
font-ttf-ubuntu           0.83                 hab24e00_0    conda-forge
fontconfig                2.14.1               hc2a2eb6_0    conda-forge
fonts-conda-ecosystem     1                             0    conda-forge
fonts-conda-forge         1                             0    conda-forge
fonttools                 4.38.0           py39hb9d737c_0    conda-forge
freetype                  2.12.1               hca18f0e_0    conda-forge
fribidi                   1.0.10               h36c2ea0_0    conda-forge
future                    0.18.2           py39hf3d152e_5    conda-forge
gcc_impl_linux-64         9.5.0               h99780fb_19    conda-forge
gcc_linux-64              9.5.0               h4258300_11    conda-forge
gettext                   0.21.1               h27087fc_0    conda-forge
gfortran_impl_linux-64    9.5.0               hf1096a2_19    conda-forge
gfortran_linux-64         9.5.0               hdb51d14_11    conda-forge
graphite2                 1.3.13            h58526e2_1001    conda-forge
gsl                       2.7                  he838d99_0    conda-forge
gxx_impl_linux-64         9.5.0               h99780fb_19    conda-forge
gxx_linux-64              9.5.0               h43f449f_11    conda-forge
harfbuzz                  4.2.0                h40b6f09_0    conda-forge
hdf5                      1.10.6          nompi_h6a2412b_1114    conda-forge
htslib                    1.14                 h9753748_2    bioconda
humanfriendly             10.0             py39hf3d152e_2    conda-forge
icu                       69.1                 h9c3ff4c_0    conda-forge
idna                      3.4                pyhd8ed1ab_0    conda-forge
importlib-metadata        4.11.4           py39hf3d152e_0    conda-forge
isa-l                     2.30.0               ha770c72_4    conda-forge
jinja2                    3.1.2              pyhd8ed1ab_1    conda-forge
jpeg                      9e                   h166bdaf_2    conda-forge
kernel-headers_linux-64   3.10.0              h4a8ded7_13    conda-forge
keyutils                  1.6.1                h166bdaf_0    conda-forge
kiwisolver                1.4.4            py39hf939315_0    conda-forge
krb5                      1.19.3               h3790be6_0    conda-forge
lcms2                     2.12                 hddcbb42_0    conda-forge
ld_impl_linux-64          2.39                 hc81fddc_0    conda-forge
lerc                      3.0                  h9c3ff4c_0    conda-forge
libblas                   3.9.0           16_linux64_openblas    conda-forge
libbrotlicommon           1.0.9                h166bdaf_7    conda-forge
libbrotlidec              1.0.9                h166bdaf_7    conda-forge
libbrotlienc              1.0.9                h166bdaf_7    conda-forge
libcblas                  3.9.0           16_linux64_openblas    conda-forge
libcurl                   7.85.0               h7bff187_0    conda-forge
libdeflate                1.10                 h7f98852_0    conda-forge
libedit                   3.1.20191231         he28a2e2_2    conda-forge
libev                     4.33                 h516909a_1    conda-forge
libffi                    3.4.2                h7f98852_5    conda-forge
libgcc-devel_linux-64     9.5.0               h0a57e50_19    conda-forge
libgcc-ng                 12.2.0              h65d4601_19    conda-forge
libgfortran-ng            12.2.0              h69a702a_19    conda-forge
libgfortran5              12.2.0              h337968e_19    conda-forge
libglib                   2.72.1               h2d90d5f_0    conda-forge
libgomp                   12.2.0              h65d4601_19    conda-forge
libiconv                  1.16                 h516909a_0    conda-forge
libidn2                   2.3.4                h166bdaf_0    conda-forge
liblapack                 3.9.0           16_linux64_openblas    conda-forge
libnghttp2                1.47.0               hdcd2b5c_1    conda-forge
libnsl                    2.0.0                h7f98852_0    conda-forge
libopenblas               0.3.21          pthreads_h78a6416_3    conda-forge
libpng                    1.6.38               h753d276_0    conda-forge
libsanitizer              9.5.0               h2f262e1_19    conda-forge
libssh2                   1.10.0               haa6b8db_3    conda-forge
libstdcxx-devel_linux-64  9.5.0               h0a57e50_19    conda-forge
libstdcxx-ng              12.2.0              h46fd767_19    conda-forge
libtiff                   4.4.0                h0fcbabc_0    conda-forge
libunistring              0.9.10               h7f98852_0    conda-forge
libuuid                   2.32.1            h7f98852_1000    conda-forge
libwebp-base              1.2.4                h166bdaf_0    conda-forge
libxcb                    1.13              h7f98852_1004    conda-forge
libxml2                   2.9.12               h885dcf4_1    conda-forge
libzlib                   1.2.13               h166bdaf_4    conda-forge
llvm-meta                 7.0.0                         0    conda-forge
lofreq                    2.1.5            py39h2515d3d_6    bioconda
lzstring                  1.0.4                   py_1001    conda-forge
make                      4.3                  hd18ef5c_1    conda-forge
markdown                  3.4.1              pyhd8ed1ab_0    conda-forge
markupsafe                2.1.1            py39hb9d737c_1    conda-forge
matplotlib-base           3.6.1            py39hf9fd14e_0    conda-forge
multiqc                   1.11               pyhdfd78af_0    bioconda
munkres                   1.1.4              pyh9f0ad1d_0    conda-forge
ncbi-ngs-sdk              2.11.2          pl5321h629fbf0_1    bioconda
ncurses                   6.2                  h58526e2_4    conda-forge
networkx                  2.8.7              pyhd8ed1ab_0    conda-forge
nextflow                  21.04.0              h4a94de4_0    bioconda
numpy                     1.23.4           py39h3d75532_0    conda-forge
openjdk                   11.0.1            h516909a_1016    conda-forge
openjpeg                  2.5.0                h7d73246_1    conda-forge
openmp                    7.0.0                h2d50403_0    conda-forge
openssl                   1.1.1q               h166bdaf_1    conda-forge
ossuuid                   1.6.2             hf484d3e_1000    conda-forge
packaging                 21.3               pyhd8ed1ab_0    conda-forge
pandoc                    2.19.2               h32600fe_1    conda-forge
pango                     1.50.7               hbd2fdc8_0    conda-forge
pbzip2                    1.1.13                        0    conda-forge
pcre                      8.45                 h9c3ff4c_0    conda-forge
pcre2                     10.37                hc3806b6_1    conda-forge
perl                      5.32.1          2_h7f98852_perl5    conda-forge
perl-alien-build          2.48            pl5321hec16e2b_0    bioconda
perl-alien-libxml2        0.17            pl5321hec16e2b_0    bioconda
perl-archive-tar          2.40            pl5321hdfd78af_0    bioconda
perl-business-isbn        3.007           pl5321hd8ed1ab_0    conda-forge
perl-business-isbn-data   20210112.006    pl5321hd8ed1ab_0    conda-forge
perl-capture-tiny         0.48            pl5321ha770c72_1    conda-forge
perl-carp                 1.50            pl5321hd8ed1ab_0    conda-forge
perl-common-sense         3.75            pl5321hd8ed1ab_0    conda-forge
perl-compress-raw-bzip2   2.201           pl5321h166bdaf_0    conda-forge
perl-compress-raw-zlib    2.202           pl5321h166bdaf_0    conda-forge
perl-constant             1.33            pl5321hd8ed1ab_0    conda-forge
perl-data-dumper          2.183           pl5321h166bdaf_0    conda-forge
perl-encode               3.19            pl5321h166bdaf_0    conda-forge
perl-exporter             5.74            pl5321hd8ed1ab_0    conda-forge
perl-exporter-tiny        1.002002        pl5321hd8ed1ab_0    conda-forge
perl-extutils-makemaker   7.64            pl5321hd8ed1ab_0    conda-forge
perl-ffi-checklib         0.28            pl5321hdfd78af_0    bioconda
perl-file-chdir           0.1011          pl5321hd8ed1ab_0    conda-forge
perl-file-path            2.18            pl5321hd8ed1ab_0    conda-forge
perl-file-temp            0.2304          pl5321hd8ed1ab_0    conda-forge
perl-file-which           1.24            pl5321hd8ed1ab_0    conda-forge
perl-importer             0.026           pl5321hd8ed1ab_0    conda-forge
perl-io-compress          2.201           pl5321h87f3376_0    bioconda
perl-io-zlib              1.11            pl5321hdfd78af_0    bioconda
perl-json                 4.10            pl5321hdfd78af_0    bioconda
perl-json-xs              2.34            pl5321h9f5acd7_5    bioconda
perl-list-moreutils       0.430           pl5321hdfd78af_0    bioconda
perl-list-moreutils-xs    0.430           pl5321hec16e2b_1    bioconda
perl-mime-base64          3.16            pl5321h166bdaf_0    conda-forge
perl-parent               0.238           pl5321hd8ed1ab_0    conda-forge
perl-path-tiny            0.124           pl5321hd8ed1ab_0    conda-forge
perl-pathtools            3.75            pl5321h166bdaf_0    conda-forge
perl-scalar-list-utils    1.63            pl5321h166bdaf_0    conda-forge
perl-scope-guard          0.21            pl5321hd8ed1ab_0    conda-forge
perl-storable             3.15            pl5321h166bdaf_0    conda-forge
perl-sub-info             0.002           pl5321hd8ed1ab_0    conda-forge
perl-term-table           0.016           pl5321hdfd78af_0    bioconda
perl-test2-suite          0.000145        pl5321hdfd78af_0    bioconda
perl-types-serialiser     1.01            pl5321hdfd78af_0    bioconda
perl-uri                  5.12            pl5321hdfd78af_0    bioconda
perl-xml-libxml           2.0207          pl5321h661654b_0    bioconda
perl-xml-namespacesupport 1.12            pl5321hd8ed1ab_0    conda-forge
perl-xml-sax              1.02            pl5321hd8ed1ab_0    conda-forge
perl-xml-sax-base         1.09            pl5321hd8ed1ab_0    conda-forge
pigz                      2.6                  h27826a3_0    conda-forge
pillow                    9.2.0            py39hd5dbb17_2    conda-forge
pip                       22.3               pyhd8ed1ab_0    conda-forge
pixman                    0.40.0               h36c2ea0_0    conda-forge
pthread-stubs             0.4               h36c2ea0_1001    conda-forge
pycparser                 2.21               pyhd8ed1ab_0    conda-forge
pygments                  2.13.0             pyhd8ed1ab_0    conda-forge
pyopenssl                 22.1.0             pyhd8ed1ab_0    conda-forge
pyparsing                 3.0.9              pyhd8ed1ab_0    conda-forge
pysocks                   1.7.1              pyha2e5f31_6    conda-forge
python                    3.9.9           h62f1059_0_cpython    conda-forge
python-dateutil           2.8.2              pyhd8ed1ab_0    conda-forge
python-isal               1.1.0            py39hb9d737c_0    conda-forge
python_abi                3.9                      2_cp39    conda-forge
pyyaml                    6.0              py39hb9d737c_4    conda-forge
r-askpass                 1.1               r41h06615bd_3    conda-forge
r-assertthat              0.2.1             r41hc72bb7e_3    conda-forge
r-backports               1.4.1             r41h06615bd_1    conda-forge
r-base                    4.1.2                h2553ce4_1    conda-forge
r-base64enc               0.1_3           r41h06615bd_1005    conda-forge
r-bit                     4.0.4             r41h06615bd_1    conda-forge
r-bit64                   4.0.5             r41h06615bd_1    conda-forge
r-blob                    1.2.3             r41hc72bb7e_1    conda-forge
r-brio                    1.1.3             r41h06615bd_1    conda-forge
r-broom                   1.0.1             r41hc72bb7e_1    conda-forge
r-bslib                   0.4.0             r41hc72bb7e_1    conda-forge
r-cachem                  1.0.6             r41h06615bd_1    conda-forge
r-callr                   3.7.2             r41hc72bb7e_1    conda-forge
r-cellranger              1.1.0           r41hc72bb7e_1005    conda-forge
r-cli                     3.4.1             r41h7525677_1    conda-forge
r-clipr                   0.8.0             r41hc72bb7e_1    conda-forge
r-colorspace              2.0_3             r41h06615bd_1    conda-forge
r-cpp11                   0.4.3             r41hc72bb7e_0    conda-forge
r-crayon                  1.5.2             r41hc72bb7e_1    conda-forge
r-curl                    4.3.3             r41h06615bd_1    conda-forge
r-data.table              1.14.4            r41h06615bd_0    conda-forge
r-dbi                     1.1.3             r41hc72bb7e_1    conda-forge
r-dbplyr                  2.2.1             r41hc72bb7e_1    conda-forge
r-desc                    1.4.2             r41hc72bb7e_1    conda-forge
r-diffobj                 0.3.5             r41h06615bd_1    conda-forge
r-digest                  0.6.30            r41h7525677_0    conda-forge
r-dplyr                   1.0.10            r41h7525677_1    conda-forge
r-dtplyr                  1.2.2             r41hc72bb7e_1    conda-forge
r-egg                     0.4.5             r41hc72bb7e_3    conda-forge
r-ellipsis                0.3.2             r41h06615bd_1    conda-forge
r-evaluate                0.17              r41hc72bb7e_1    conda-forge
r-fansi                   1.0.3             r41h06615bd_1    conda-forge
r-farver                  2.1.1             r41h7525677_1    conda-forge
r-fastmap                 1.1.0             r41h7525677_1    conda-forge
r-forcats                 0.5.2             r41hc72bb7e_1    conda-forge
r-fs                      1.5.2             r41h7525677_2    conda-forge
r-gargle                  1.2.1             r41hc72bb7e_1    conda-forge
r-generics                0.1.3             r41hc72bb7e_1    conda-forge
r-ggplot2                 3.3.6             r41hc72bb7e_1    conda-forge
r-glue                    1.6.2             r41h06615bd_1    conda-forge
r-googledrive             2.0.0             r41hc72bb7e_1    conda-forge
r-googlesheets4           1.0.1             r41h785f33e_1    conda-forge
r-gridextra               2.3             r41hc72bb7e_1004    conda-forge
r-gtable                  0.3.1             r41hc72bb7e_1    conda-forge
r-haven                   2.5.1             r41h7525677_0    conda-forge
r-highr                   0.9               r41hc72bb7e_1    conda-forge
r-hms                     1.1.2             r41hc72bb7e_1    conda-forge
r-htmltools               0.5.3             r41h7525677_1    conda-forge
r-httr                    1.4.4             r41hc72bb7e_1    conda-forge
r-ids                     1.0.1             r41hc72bb7e_2    conda-forge
r-isoband                 0.2.6             r41h7525677_1    conda-forge
r-jquerylib               0.1.4             r41hc72bb7e_1    conda-forge
r-jsonlite                1.8.3             r41h06615bd_0    conda-forge
r-knitr                   1.40              r41hc72bb7e_1    conda-forge
r-labeling                0.4.2             r41hc72bb7e_2    conda-forge
r-lattice                 0.20_45           r41h06615bd_1    conda-forge
r-lifecycle               1.0.3             r41hc72bb7e_1    conda-forge
r-lubridate               1.8.0             r41h7525677_1    conda-forge
r-magrittr                2.0.3             r41h06615bd_1    conda-forge
r-mass                    7.3_58.1          r41h06615bd_1    conda-forge
r-matrix                  1.5_1             r41h5f7b363_0    conda-forge
r-memoise                 2.0.1             r41hc72bb7e_1    conda-forge
r-mgcv                    1.8_41            r41h5f7b363_0    conda-forge
r-mime                    0.12              r41h06615bd_1    conda-forge
r-modelr                  0.1.9             r41hc72bb7e_1    conda-forge
r-munsell                 0.5.0           r41hc72bb7e_1005    conda-forge
r-nlme                    3.1_160           r41h8da6f51_0    conda-forge
r-openssl                 2.0.4             r41hfaab4ff_0    conda-forge
r-openxlsx                4.2.5             r41h7525677_1    conda-forge
r-pillar                  1.8.1             r41hc72bb7e_1    conda-forge
r-pkgconfig               2.0.3             r41hc72bb7e_2    conda-forge
r-pkgload                 1.3.0             r41hc72bb7e_1    conda-forge
r-praise                  1.0.0           r41hc72bb7e_1006    conda-forge
r-prettyunits             1.1.1             r41hc72bb7e_2    conda-forge
r-processx                3.7.0             r41h06615bd_1    conda-forge
r-progress                1.2.2             r41hc72bb7e_3    conda-forge
r-ps                      1.7.1             r41h06615bd_1    conda-forge
r-purrr                   0.3.5             r41h06615bd_1    conda-forge
r-r6                      2.5.1             r41hc72bb7e_1    conda-forge
r-rappdirs                0.3.3             r41h06615bd_1    conda-forge
r-rcolorbrewer            1.1_3             r41h785f33e_1    conda-forge
r-rcpp                    1.0.9             r41h7525677_2    conda-forge
r-readr                   2.1.3             r41h7525677_1    conda-forge
r-readxl                  1.4.1             r41hf23e330_0    conda-forge
r-rematch                 1.0.1           r41hc72bb7e_1005    conda-forge
r-rematch2                2.1.2             r41hc72bb7e_2    conda-forge
r-reprex                  2.0.2             r41hc72bb7e_1    conda-forge
r-rlang                   1.0.6             r41h7525677_1    conda-forge
r-rmarkdown               2.17              r41hc72bb7e_1    conda-forge
r-rprojroot               2.0.3             r41hc72bb7e_1    conda-forge
r-rstudioapi              0.14              r41hc72bb7e_1    conda-forge
r-rvest                   1.0.3             r41hc72bb7e_1    conda-forge
r-sass                    0.4.2             r41h7525677_1    conda-forge
r-scales                  1.2.1             r41hc72bb7e_1    conda-forge
r-selectr                 0.4_2             r41hc72bb7e_2    conda-forge
r-stringi                 1.7.6             r41h337692f_1    conda-forge
r-stringr                 1.4.1             r41hc72bb7e_1    conda-forge
r-sys                     3.4.1             r41h06615bd_0    conda-forge
r-testthat                3.1.5             r41h7525677_1    conda-forge
r-tibble                  3.1.8             r41h06615bd_1    conda-forge
r-tidyr                   1.2.1             r41h7525677_1    conda-forge
r-tidyselect              1.2.0             r41hc72bb7e_0    conda-forge
r-tidyverse               1.3.2             r41hc72bb7e_1    conda-forge
r-tinytex                 0.42              r41hc72bb7e_1    conda-forge
r-tzdb                    0.3.0             r41h7525677_1    conda-forge
r-utf8                    1.2.2             r41h06615bd_1    conda-forge
r-uuid                    1.1_0             r41h06615bd_1    conda-forge
r-vctrs                   0.5.0             r41h7525677_0    conda-forge
r-viridislite             0.4.1             r41hc72bb7e_1    conda-forge
r-vroom                   1.6.0             r41h7525677_1    conda-forge
r-waldo                   0.4.0             r41hc72bb7e_1    conda-forge
r-withr                   2.5.0             r41hc72bb7e_1    conda-forge
r-xfun                    0.34              r41h7525677_0    conda-forge
r-xml2                    1.3.3             r41h03ef668_0    conda-forge
r-yaml                    2.3.6             r41h06615bd_0    conda-forge
r-zip                     2.2.1             r41h06615bd_1    conda-forge
readline                  8.1                  h46c0cb4_0    conda-forge
requests                  2.28.1             pyhd8ed1ab_1    conda-forge
rich                      12.6.0             pyhd8ed1ab_0    conda-forge
samtools                  1.14                 hb421002_0    bioconda
sed                       4.8                  he412f7d_0    conda-forge
seqtk                     1.3                  h7132678_4    bioconda
setuptools                65.5.0             pyhd8ed1ab_0    conda-forge
simplejson                3.17.6           py39hb9d737c_1    conda-forge
six                       1.16.0             pyh6c4a22f_0    conda-forge
spades                    3.15.5               h95f258a_1    bioconda
spectra                   0.0.11                     py_1    conda-forge
sqlite                    3.37.0               h9cd32fc_0    conda-forge
sra-tools                 2.11.0          pl5321ha49a11a_3    bioconda
sysroot_linux-64          2.17                h4a8ded7_13    conda-forge
tbb                       2021.6.0             h924138e_0    conda-forge
tk                        8.6.12               h27826a3_0    conda-forge
tktable                   2.10                 hb7b940f_3    conda-forge
typing_extensions         4.4.0              pyha770c72_0    conda-forge
tzdata                    2022e                h191b570_0    conda-forge
unicodedata2              14.0.0           py39hb9d737c_1    conda-forge
urllib3                   1.26.11            pyhd8ed1ab_0    conda-forge
wget                      1.20.3               ha56f1ee_1    conda-forge
wheel                     0.37.1             pyhd8ed1ab_0    conda-forge
xopen                     1.6.0            py39hf3d152e_0    conda-forge
xorg-kbproto              1.0.7             h7f98852_1002    conda-forge
xorg-libice               1.0.10               h7f98852_0    conda-forge
xorg-libsm                1.2.3             hd9c2040_1000    conda-forge
xorg-libx11               1.7.2                h7f98852_0    conda-forge
xorg-libxau               1.0.9                h7f98852_0    conda-forge
xorg-libxdmcp             1.1.3                h7f98852_0    conda-forge
xorg-libxext              1.3.4                h7f98852_1    conda-forge
xorg-libxrender           0.9.10            h7f98852_1003    conda-forge
xorg-libxt                1.2.1                h7f98852_2    conda-forge
xorg-renderproto          0.11.1            h7f98852_1002    conda-forge
xorg-xextproto            7.3.0             h7f98852_1002    conda-forge
xorg-xproto               7.0.31            h7f98852_1007    conda-forge
xz                        5.2.6                h166bdaf_0    conda-forge
yaml                      0.2.5                h7f98852_2    conda-forge
zipp                      3.10.0             pyhd8ed1ab_0    conda-forge
zlib                      1.2.13               h166bdaf_4    conda-forge
zstd                      1.5.2                h6239696_4    conda-forge


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
amrfinder_update --database /home/data_for_classes/bakta_database/db/amrfinderplus-db ?
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

