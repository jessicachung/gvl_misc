```
General bugs:
Can't import history from an archive when history is empty

Trackster:
/mnt/galaxy/tmp/job_working_directory/000/25/galaxy_25.sh: 39: /mnt/galaxy/tmp/job_working_directory/000/25/galaxy_25.sh: bedGraphToBigWig: not found


```
----------------------------------------------------------------------
# Intro to Galaxy

## Get data
 - Register and login
 - File format: 'tablular' (can auto-detect)  
   Upload contig_stats.txt (local)
 - File Format: 'fastqsanger'
   `https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/COMP90014/Assignment1/bacterial_std_err_1.fastq.gz`
 - Get microbial data `#NOTE: broken, won't be fixed. Remove from tutorial`
 - Rename fastq to 'MRSA252.fna' `#NOTE: change to rename *.fq`

---

## Histogram and summary statistics

### Cut out column 1 and 6
 - Text Manipulation > Cut  `#NOTE: two cut tools in the toolbar #IN-TEST`  
   Columns: "c1,c6"  
   Delimited by: Tab  
   Contig_stats.txt  

### Remove the Header lines of the new file.
 - Text Manipulation > Remove beginning `#NOTE: two tools in the toolbar #IN-TEST`  
   Remove First: 1  
   From: Cut on data1

### Make a histogram.
 - Graph/Display Data > Histogram `#NOTE: Histogram appears in Statistics and not Graph/Display Data #IN-TEST`  
   Dataset: Remove beginning on Data X  
   Numerical column for X axis: c2  
   Number of breaks: 25  
   Plot title: Histogram of Contig Coverage  
   Label for X axis: Coverage depth

### Calculate summary statistics for contig coverage depth.
 - Statistics > Summary Statisitics  
   Summary statistics on: Remove beginning on Data X
   Column or expression: c2

```
#FIXME: broken
Traceback (most recent call last):
  File "/mnt/galaxy/galaxy-app/tools/stats/gsummary.py", line 8, in <module>
    from rpy import *
ImportError: No module named rpy
```

---

### Convert Fastq to Fasta
 - Convert Formats -> FASTQ to FASTA  `#NOTE: tool appears in NGS: QC and manipulation #IN-TEST`
`#NOTE: add rename to *.fna`

### Find Ribosomal RNA Features in a DNA Sequence
 - Annotation -> barrnap  
   Fasta file: MRSA252.fna

### Filter for 23S rRNA annotations
 - Filter and Sort -> Select  
   Select lines from:  barnap file  
   the pattern: 23S
`#FIXME: Get an empty file. Nothing matching to 23S`

----------------------------------------------------------------------
# Intermediate Galaxy

`#WIP`


----------------------------------------------------------------------

# RNA-Seq basic

## Get data
 - Register and login
 - File Format: 'fastqsanger':
```
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/C1_R1.chr4.fq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/C1_R2.chr4.fq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/C1_R3.chr4.fq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/C2_R1.chr4.fq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/C2_R2.chr4.fq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/C2_R3.chr4.fq
```
 - Upload:
   `https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/ensembl_dm3.chr4.gtf`

---
## Alignment
 - Tophat
   Reference genome: D. melanogaster (dm3)  
   Single-end  
   Multipe datasets: select all 6 fastq files `#NOTE: update the tutorial to use multiple selection`  
   Use defaults for other fields

---
# IGV
 - display with IGV: web current  
   chr4

---
## Cuffdiff
 - NGS: RNA Analysis>Cuffdiff  
   Transcripts: ensembl_dm3.chr4.gtf  
   Condition 1 (C1): 1,2,3  
   Condition 2 (C2): 4,5,6  
   Keep other defaults

---
## Inspect the gene differential expression testing file
 - Filter and Sort>Filter  
   Select 'Cuffdiff ... gene differential expression testing'  
   Condition: c14=='yes'

 - Results:  
   ‘Ank’ from chr4:137014-150378  
   ‘CG2177’ from chr4:331557-334534  
   Q-values 0.00175 (column 13) for each.

----------------------------------------------------------------------
# RNA-Seq Advanced

`#WIP`


----------------------------------------------------------------------
# Variant Calling Basic

## Get data
 - File Format: 'fastqsanger'  
   `https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/VariantDet_BASIC/NA12878.GAIIx.exome_chr22.1E6reads.76bp.fastq`

---
## QC
 - NGS: QC and manipulation>FASTQC: Read QC `#NOTE: tool renamed to FastQC: Comprehensive QC`

---
## Alignment [20 mins]
 - NGS: Mapping>Map with BWA for Illumina [3-5mins]  
   Use the default FASTQ file  
   Select the 'hg19' reference genome  
   Keep all other defaults  `#NOTE: change to Single fastq`

```
#NOTE: Need to change tutorial steps. BWA outputs BAM format.
Need to add step to convert to SAM or filter BAM without converting

Maybe change step to:
NGS: SAM Tools > Filter SAM or BAM, output SAM or BAM
Select BAM file
Select regions (only used when the input is in BAM format): chr22
Result: 1075182 reads
```

### Filter non chromosome 22 alignments out of the SAM file
 - Filter and Sort>Filter  
   Use 'c3==”chr22”'
 - Results: 93.4% mapped reads kept (93.35% of 1151797 valid lines)
`#NOTE: Need to manually specify how many header lines to skip otherwise can't convert to bam later`

### Convert the SAM file to BAM file
 - NGS: Sam Tools>SAM-to-BAM [1 min]
`#NOTE: Need to manually change sam database/build as hg19 to be able to use SAM-to-BAM. This might be fixed with bwa loc files or by using bwa-mem`

### Check quality metrics
 - NGS: Sam Tools>Flagstat
 - Result: 1075182 reads

### Visualization
 - Display with IGV web current
`#NOTE: need to manually specify bam as hg19 otherwise web_current option doesn't appear in 'display with IGV'. This might be fixed with bwa loc files or by using bwa-mem`  
   Select chr22
 - check chr22:35,783,964-35,821,038  
   check chr22:35,947,405-35,947,867
 - (Don't close IGV yet)

---
## Calling single nucleotide variations (SNVs) [20 min]

### Generate a pileup file
 - NGS: SAMtools>Generate Pileup [2-3mins]  
   Call consensus according to MAQ model = Yes
 - Change new filetype to 'pileup'

### Filter pileup file
 - NGS: SAM Tools>Filter Pileup [3-5mins]  
   Select 'Pileup with ten columns (with consensus)'  
   Do not report positions with coverage lower than = 10  
   Convert coordinates to intervals = Yes
 - Result: from ~9.9M SNPs to ~16K SNPs

### Filter by SNP quality
 - Filter and Sort>Filter  
   Filter on SNP quality (column 7): 'c7>50'  
   Result: 891 SNPs `#NOTE: got 892 SNPs`

### Convert the pileup file to BED format and view in IGV
 - Change datatype to bed
 - Download the BED file
 - Open the file in IGV
 - Try chr22:32,154,733-39,745,059  
   Try chr22:36,121,873-36,124,616  
   Hets: chr22:36,006,155-36,008,007  
   Hom: chr22:35,947,405-35,947,867

---
## Calling small insertions and deletions (indels) [20 min]

### Extract indels from SAM
 - NGS: Indel Analysis>Indel Analysis `#FIXME: Missing tool`  
 - Rename the file with 600 regions to ‘NA12878_deletions’ and change it’s datatype to BED
 - Rename the file with 575 regions to ‘NA12878_insertions’ and change it’s datatype to BED

### Filter by #reads
 - Filter and Sort>Filter  
   'c5>10' for the ‘NA12878_insertions’ file  
 - Result: There should be 15 regions
 - View in IGV  
   Try chr22:31,854,318-31,854,553

```
Complete history (1.6 GB):
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/VariantDet_BASIC/Galaxy-History-VariantDet_BASIC_Complete.tar.gz
```

----------------------------------------------------------------------
# Variant Calling Advanced

## Get data
 - File format: 'fastqsanger'
 ```
https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/variantCalling_ADVNCD/NA12878.hiseq.wgs_chr20_2mb.30xPE.fastq_1
https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/variantCalling_ADVNCD/NA12878.hiseq.wgs_chr20_2mb.30xPE.fastq_2
```
 - Upload
   `https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/variantCalling_ADVNCD/dbSNP135_excludingsitesafter129_chr20.vcf`

## QC

## Mapping
 - NGS: Mapping>Map with BWA for Illumina [2-3mins]  
   hg19  
   Paired End  
   Forward FASTQ File:  NA12878.hiseq.wgs_chr20_2mb.30xPE.fastq_1  
   Reverse FASTQ File:  NA12878.hiseq.wgs_chr20_2mb.30xPE..fastq_2  
   BWA settings to use: Full Parameter List  
   Specify the read group for this file? (samse/sampe -r): Yes
   Read Group Identifier (ID): Tutorial_readgroup  
   Library name (LB): Tutorial_library  
   Platform/technology used to produce the reads (PL): ILLUMINA  
   Sample (SM): NA12878  
`#NOTE: need to update inputs with changed BWA options`
`#NOTE: change ID to 'bwa' when mapping or GATK won't work`
`#NOTE: don't need to convert to BAM, but need to specify hg19 for IGV.
BWA-mem automatically sets the file to hg19`

## Visualization
 - IGV  
   try chr20:1,870,686-1,880,895

## Generate a genomic interval
 - Text manipulation>Create single interval  
   Chromosome: chr20  
   Start position: 0  
   End position: 2000000  
   Name: chr20_2mb  
   Strand: plus

## Evaluate the depth of coverage
 - NGS: GATK2 Tools>Depth of Coverage [1-2 min]  
   BAM files: Select the BAM file you just generated  
   Using reference genome: Human (hg19)  
   Output format: table  
   Basic or Advanced GATK options: Advanced  
   Add new Operate on Genomic intervals  
   Select the chr20_2mb.bed file from your history

```
#NOTE: change ID to 'bwa' when mapping

From GATK 2.8:
##### ERROR
##### ERROR MESSAGE: SAM/BAM file /mnt/galaxy/tmp/tmp-gatk-UpuOl6/gatk_input_0.bam is malformed: Error parsing SAM header. Problem parsing @PG key:value pair ID:Tutorial_readgroup clashes with ID:bwa. Line:
##### ERROR @PG	ID:bwa	PN:bwa	VN:0.7.10-r876-dirty	CL:bwa sampe -r @RG	ID:Tutorial_readgroup	SM:NA12878	PL:ILLUMINA	LB:Tutorial_library /mnt/galaxyIndices/genomes/hg19/bwa_mem_index/hg19/hg19.fa first.sai second.sai /mnt/galaxy/files/000/dataset_48.dat /mnt/galaxy/files/000/dataset_49.dat; File /mnt/galaxy/tmp/tmp-gatk-UpuOl6/gatk_input_0.bam; Line number 96

```

```
output summary sample:
Target	total_coverage	average_coverage	NA12878_total_cvg	NA12878_mean_cvg	NA12878_granular_Q1	NA12878_granular_median	NA12878_granular_Q3	NA12878_%_above_15
chr20:1-2000000	48169937	24.83	48169937	24.83	21	26	31	92.5
```

---
## SNV Calling

 - NGS:SAM Tools>Mpileup [1-2 min]  
   BAM file: The BAM file you generated in Section 2  
   Using reference genome: hg19  
   Genotype Likelihood Computation: Perform a genotype likelihood computation  
   Perform INDEL calling: Do not perform indel calling  
   Set advanced options: Advanced  
   List of regions or sites on which to operate: chr20.bed

### Convert the BCF file to a VCF file  
 - NGS:SAM Tools> bcftools view
   Choose a bcf file to view: Mpileup file just generated ‘MPileup on data... and data …’
   Keep other defaults
 - Rename the file to something useful eg ‘NA12878.Mpileup.chr20_2mb.vcf’
 - Change the output format from tabular to vcf
 - Result: ~3000 variants

### IGV
 - Try chr20:1,123,714-1,126,378

----------------------------------------------------------------------
# Assembly

## Get data
 - Datatype: fastqsanger
```
https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/Assembly/ERR048396_1.fastq.gz
https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/Assembly/ERR048396_2.fastq.gz
```
 - Datatype: fasta
   `https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/Assembly/illumina_adapters.fna`

---
## QC

### FastQC
 - NGS QC and manipulation > FastQ QC -> FastQC:ReadQC `#NOTE: rename to FastQC: Comprehensive QC`  
   FASTQ reads: eg. ERR048396_1.fastq

### Quality trim the reads
 - NGS QC and manipulation > Trimmomatic `#FIXME: change trimmomatic to Simon's test-toolshed version`  
   Direction 1 fastq reads to trim: ERR048396_1.fastq  
   Direction 1 fastq reads to trim: ERR048396_2.fastq  
   Quality encoding: phred33  
   Clip Illumina adapters? checkbox: checked  
   Fasta of adapters to clip: illumina_adapters.fa  
   Trim leading bases, minimum quality: 15  
   Trim trailing bases, minimum quality: 15  
   Minimum length read: 35

---
## De novo assembly

### Assembly
 - NGS: Assembly > Velvet Optimiser  `#NOTE: change name to VelvetOptimiser and change options`  
   Start k-mer value: 55  
   End k-mer value: 69  
   Click the “Add new Input read libraries” button.  
   File type selector: shortPaired  
   Are the reads paired and in two separate files? checkbox: checked  
   Read dataset for direction 1: 7: Trimmomatic on da..immed pairs  
   Read dataset for direction 2: 8: Trimmomatic on da..immed pairs  
   Click the “Add new Input read libraries” button.  
   Read dataset: 9: Trimmomatic on da..immed reads

### Stats
 - FASTA Manipulation > Fasta Statistics
   Fasta or multifasta file: Velvet Optimiser .. 7: Contigs

---

## Extension

### Examine the contig coverage depth
 - Filter and Sort > Filter tool.
   Contig stats
   c2 > 100


----------------------------------------------------------------------
