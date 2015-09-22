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
#FIXME
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

## Get data
 - fastqsanger format
```
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/batch1_chrI_1.fastq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/batch1_chrI_2.fastq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/batch2_chrI_1.fastq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/batch2_chrI_2.fastq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/batch3_chrI_1.fastq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/batch3_chrI_2.fastq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/chem1_chrI_1.fastq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/chem1_chrI_2.fastq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/chem2_chrI_1.fastq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/chem2_chrI_2.fastq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/chem3_chrI_1.fastq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/chem3_chrI_2.fastq
```
`#NOTE: having trouble uploading all 12 files at once. Only the first 9 files get uploaded. Need another batch for the remaining 3.`
 - Upload
   `https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/genes.gtf`

## Alignment
 - NGS: RNA Analysis>Tophat2 `#NOTE: the tool is named Tophat (but is still tophat2)`  
   Is this library mate-paired: Paired-end  
   RNA-Seq FASTQ file, forward reads: batch1_chrI_1.fastq `#NOTE: change to use multiple datasets (1,3,5,7,9,11)`  
   RNA-Seq FASTQ file, reverse reads: batch1_chrI_2.fastq `#NOTE: change to use multiple datasets (2,4,6,8,10,12)`  
   Use a built in reference genome or own from your history: Use a built-in genome  
   Select a reference genome: saccharomyces cerevisiae sacCer2  
   Keep all other defaults  
 - When complete, rename the accepted-hits file into a more meaningful name - eg 'batch1-accepted_hits.bam'  
 - View an aligned BAM file in Trackster `#FIXME: or change to IGV`  
   Try chrI:148518-153621  
   Try chrI:86985-87795  

## Cuffdiff
 - NGS: RNA Analysis > Cuffdiff  
   Transcripts: genes.gtf  
   Conditions > Condition 1 > Name: batch  
   Add 3 batch replicates `#NOTE: change to add multiple datasets`  
   Add new group>Condition 2>Name: chem  
   Add 3 chem replicates  
   Keep rest defaults  
 - Filter and Sort > Filter  
   Filter: ‘Cuffdiff on data ... and others: gene differential expression testing’  
   With following condition: c14=='yes'  
   Number of header lines to skip: 1  
 - Rename the filter file to a more meaningful name. eg: CuffDiff-SignificantlyExpressedGenes  
 - Result: 53 differentially expressed genes

## Count reads in Features
 - NGS: SAM Tools>BAM-to-SAM
   BAM File to Convert: batch1-accepted_hits.bam
   Include header in output
```
#NOTE: Do we still need to do this?
From tutorial: "*We convert from bam to sam format because the tool ‘htseq-count’
currently works a little more reliably with sam files."
I ran htseq-count using both bam and sam files, and got the same result for both.
```
 - NGS: RNA Analysis > SAM/BAM to count matrix  
   Gene model (GFF) file to count reads over from your current history: genes.gtf  
   bam/sam file from your history: batch1: converted SAM  
   Additional bam/sam file from your history: batch2: converted SAM  
   Specify additional bam/sam file inputs 1 -> Additional bam/sam file from your history: batch3: converted SAM
   `#NOTE: change to add multiple datasets`
   Keep rest defaults

## Differential gene expression analysis using EdgeR
 - NGS RNA analysis -> Differential_Count  
   Select an input matrix - rows are contigs, columns are counts for each sample: bams to DGE count matrix_htseqsams2mx.xls
   Title for job outputs: Differential Counts-EdgeR
   Treatment Name: Batch
   Select columns containing treatment.: tick c2: Batch1-sam, c3: Batch2-sam, c4: Batch3-sam
   Control Name: Chem
   Select columns containing control.: tick c5: Chem1-sam, c6: Chem2-sam, c7: Chem3-sam
   Run this model using edgeR
   Keep rest defaults
 - Filter and Sort -> Filter
   Filter: DifferentialCounts_topTable_edgeR.xls
   With following condition: c6 <= 0.05
   Number of header lines to skip: 1
 - Result: 55 differentially expressed genes


`#NOTE: not seeing generated MDS plot or heatmap from Differential_Count tool with edgeR, but can see with DESeq2`

## Differential expression with DESeq2
 - NGS RNA analysis -> Differential_Count  
   Select DESeq2 instead of EdgeR
 - Filter  
   c6 <= 0.05  
   Number of header lines to skip: 1
 - Result: 55 genes

## Look at overlap
 - Graph/Display Data -> proportional venn  
   title: CommonSection
   input file 1: CuffDiff-SignificantlyExpressedGenes
   column index: 2
   as name: CuffDiff
   input file 2: EdgeR-SignificantlyExpressedGenes
   column index file 2: 0
   as name: EdgeR
   two or three: three
   input file 3: Deseq-SignificantlyExpressedGenes
   column index file 3: 0
   as name file 3: Deseq

```
#FIXME:
Traceback (most recent call last):
  File "/mnt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/idot/prop_venn/cc6707a1e044/prop_venn/venner.py", line 12, in <module>
    from mako.template import Template
ImportError: No module named mako.template
```

 - Text Manipulation -> cut
   filtered cuffdiff (c3)
 - Text Manipulation -> cut
   filtered edgeR (c1)
 - Text Manipulation -> cut
   filtered deseq2 (c1)
 - Join, Subtract and Group -> Compare two Datasets `NOTE: tool in Join, Subtract and Group`
   Compare: cuffDiff-Column1
   against: EdgeR-Column1
 - Rename to something more meaningful: eg Common genes CuffDiff and EdgeR
 - Join, Subtract and Group -> Compare two Datasets
   Compare: Common genes CuffDiff and EdgeR
   against: Deseq-Column1
 - Rename to Common genes all methods.
 - Result: 50 genes

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
   Read group identifier (ID): bwa `NOTE: This needs to be bwa or gatk will complain`
   Read group sample name (SM): NA12878
   Library name (LB): Tutorial_LB

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
 - Result: Coverage should be ~24x


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

```
bwa-mem, gatk2 output summary sample:
sample_id	total	mean	granular_third_quartile	granular_median	granular_first_quartile	%_bases_above_15
NA12878	48268897	24.88	31	26	21	92.5
Total	48268897	24.88	N/A	N/A	N/A
```

```
bwa-mem, gatk1 output summary sample:
sample_id	total	mean	granular_third_quartile	granular_median	granular_first_quartile	%_bases_above_15
NA12878	48267005	24.13	31	26	21	89.7
Total	48267005	24.13	N/A	N/A	N/A
```

---
## SNV Calling: Mpileup `#FIXME: This section doesn't work`

 - NGS:SAM Tools>Mpileup [1-2 min]  
   BAM file: The BAM file you generated in Section 2  
   Using reference genome: hg19  
   Genotype Likelihood Computation: Perform a genotype likelihood computation  
   Perform INDEL calling: Do not perform indel calling  
   Set advanced options: Advanced  
   List of regions or sites on which to operate: chr20.bed
   ```
   #NOTE:
   BCF
   Compressed
   Select regions to call: From an uploaded BED file (--positions)
   BED file: chr20:bed

   #FIXME:
   Mpileup fails when using BCF. Known bug in galaxy:
   https://trello.com/c/cYkUbipj/2734-error-samtools-mpileup-form-problems-formerly-metadata-issue
   Fatal error: Exit code 1 (Error)
   (Can get VCF output file but fails with multiple output fields)
   ```

### Convert the BCF file to a VCF file  
 - NGS:SAM Tools> bcftools view `#NOTE: There are two tools called bcftools view. `
   Choose a bcf file to view: Mpileup file just generated ‘MPileup on data... and data …’
   Keep other defaults
 - Rename the file to something useful eg ‘NA12878.Mpileup.chr20_2mb.vcf’
 - Change the output format from tabular to vcf
 - Result: ~3000 variants

### IGV
 - Try chr20:1,123,714-1,126,378

---

## Local Realignment
 - GATK2 Tools> Realigner Target Creator  
   BAM file: Select the BAM file from previous section  
   Using reference genome: Human (hg19)  
   Basic or Advanced GATK options: Advanced  
   Add new Operate on Genomic intervals: Select the chr20_2mb.bed file from your history
   Result: ~2800 regions have been identified as candidates for local realignment `#NOTE: obtained 1,506 lines`
 - NGS: GATK2 Tools> Indel Realigner  
   BAM file: Select the BAM file from previous section  
   Using reference genome: Human (hg19)  
   Restrict realignment to provided intervals: use the intervals file generated in the previous step
 - Rename the file to something useful eg 'NA12878.chr20_2mb.30xPE.realigned'

### IGV
 - Open the realigned BAM file in IGV `#NOTE: using web_current won't make the bam files show in the same window`
   It should appear as a new frame below the already opened previous BAM
   Compare the realigned BAM file to original BAM around an indel
   check region chr20:1163914-1163954
 - Result: reads originally providing evidence of a ‘G/C’ variant at chr20:1163937 to be realigned with a 10bp insertion at chr20:1163835 and no evidence of the variant

---

## FreeBayes [10 min]

 - NGS: Variation Detection -> Free Bayes `#NOTE: Need to change parameters`
   BAM file: 'NA12878.chr20_2mb.30xPE.realigned'
   Using reference genome: hg19
   Basic or Advanced options: Advanced
   Limit analysis to listed targets: Limit By target File
   Limit analysis to targets listed in the BED-format FILE: chr20_2mb.bed
   Set allele scope options: Set
   Ignore insertion and deletion alleles: check
   Set input filters options: Set
   Exclude reads with more than N base mismatches, ignoring gaps with quality >= mismatch-base-quality-threshold: 1    
   Keep all other defaults
   ```
   Set allelic scope? Yes
   Ignore indels alleles: Yes
   Set input filters? Yes
   Perform mismatch filtering? Yes
   Exclude reads with more than N base mismatches, ignoring gaps with quality >= Q (third option abobe): 1
   ```
   ```
    Traceback (most recent call last):
      File "/mnt/galaxy/galaxy-app/lib/galaxy/jobs/runners/__init__.py", line 163, in prepare_job
        job_wrapper.prepare()
      File "/mnt/galaxy/galaxy-app/lib/galaxy/jobs/__init__.py", line 859, in prepare
        self.command_line, self.extra_filenames, self.environment_variables = tool_evaluator.build()
      File "/mnt/galaxy/galaxy-app/lib/galaxy/tools/evaluation.py", line 422, in build
        raise e
    NotFound: cannot find 'report_monomorphic' while searching for 'options_type.optional_inputs.report_monomorphic'

    Works if Choose parameter selection level is set to: 1:Simple diploid calling
    4,243 lines, 57 comments
```
 - Rename to ‘NA12878.FreeBayes.chr20_2mb.vcf’
   Check the generated list of variants
   Note there are ~6100 variants in this list
   Open the VCF file in IGV, using the link in the history panel
   Find a region where FreeBayes has called a variant but Mpileup hasn’t
   Try chr20:1,123,714-1,128,378
   Note that FreeBayes does not do any filtering, but rather leaves the user to filter based on e.g. variant quality scores. So it returns many more variants, many of which will be low quality. In other words, FreeBayes will call a potential variant based on much less evidence than MPileup.
   FreeBayes gives you more false positives
   Mpileup will give you more false negatives

---
## Unified Genotyper

 - NGS: GATK2 Tools ->Unified Genotyper
   BAM file: Realigned BAM file ‘NA12878.chr20_2mb.30xPE.realigned’
   Using reference genome: Human (hg19)
   dbSNP ROD file: dbsnp135_excludingsitesafter129_chr20.vcf
   Genotype likelihoods calculation model to employ: SNP
   Basic or Advanced GATK options: Advanced
   Add new Operate on Genomic intervals:
   List of regions or sites on which to operate: chr20_2mb
 - Rename the file to something useful eg ‘NA12878.GATK.chr20_2mb.vcf’
 - Note there are ~3200 variants in this list (3,189 lines)
 - Open the VCF file in IGV, using the link in the history panel
   Find a regions where GATK has called a variant but FreeBayes/mpileup hasn’t, and vice versa
   Try chr20:1,123,714-1,128,378
   We now have three different variant call sets from the same data. Browse around in IGV looking at places where they are different: eg look at: chr20:1,127,767-1,127,906
   Here each of the variant callers has given a different SNP call. This is a confusing region for SNP callers due to the 15bp deletion, which may or may not be on both alleles.
   We could immediately improve the FreeBayes callset by filtering on variant quality scores. If you want, you can do this with the NGS: GATK>Select Variants tool, choose the FreeBayes vcf file, click ‘Add new criteria to use when selecting data.’, use ‘QUAL>50’, Execute)

---
## Evaluate known variants
 - Evaluate dbSNP concordance and Ti/Tv ratio using the GATK VariantEval tool
   NGS: GATK2 Tools -> Eval Variants
   Variant 1: NA12878.Mpileup.chr20_2mb.vcf
   Add new Variant
   Variant 2: NA12878.FreeBayes.chr20_2mb.vcf
   Add new Variant
   Variant 3: NA12878.UnifiedGeno.chr20_2mb.vcf
   Using reference genome: Human (hg19)
   Provide a dbsnp reference-ordered data file: set dbSNP
   dbSNP ROD file: dbSNP135_excludingsitesafter129.chr20.vcf
   Basic or Advanced Analysis options: Advanced
   Eval modules to apply on the eval track(s):
   CompOverlap
   TiTvVariantEvaluator
   Do not use the standard eval modules by default: check
   Execute
   When finished, change the Datatype of the generated Eval Variant (report) to ‘txt’

### Venn diagram
`#FIXME`

### Annotation
`#FIXME`

`#NOTE: This tutorial seems too long`


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
