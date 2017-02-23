# Tool testing GVL 4.1

********************************************************************************

## Basic Variant Calling

Fastqsanger
```
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/VariantDet_BASIC/NA12878.GAIIx.exome_chr22.1E6reads.76bp.fastq
```

Then run workflow `vc_basic`
-----


## Advanced Variant Calling

Fastqsanger
```
https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/variantCalling_ADVNCD/NA12878.hiseq.wgs_chr20_2mb.30xPE.fastq_1
https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/variantCalling_ADVNCD/NA12878.hiseq.wgs_chr20_2mb.30xPE.fastq_2

https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/variantCalling_ADVNCD/dbSNP135_excludingsitesafter129_chr20.vcf
```

Then run workflow `vc_adv`

Manually download SnpEff database then run SnpEff

-----

## Basic RNA-seq

```
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/C1_R1.chr4.fq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/C1_R2.chr4.fq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/C1_R3.chr4.fq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/C2_R1.chr4.fq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/C2_R2.chr4.fq
https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/C2_R3.chr4.fq

https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_BASIC/ensembl_dm3.chr4.gtf
```

Tophat align

-----

## Advanced RNA-seq

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

https://swift.rc.nectar.org.au:8888/v1/AUTH_a3929895f9e94089ad042c9900e1ee82/RNAseqDGE_ADVNCD/genes.gtf
```

Reference: SacSer2

Align with Tophat
Cuffdiff
SAM/BAM to count matrix
Differential_Count
 - DESeq2
 - EdgeR

prop_venn tool already tested previously

Also test:
 - htseq-count
 - DESeq2
-----

## Microbial assembly and FASTA manipulation

```
https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/Assembly/ERR048396_1.fastq.gz
https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/Assembly/ERR048396_2.fastq.gz

https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/Assembly/illumina_adapters.fna
```

********************************************************************************

## Text manipulation

```
https://swift.rc.nectar.org.au:8888/v1/AUTH_377/public/galaxy101/Contig_stats.txt.gz
```

Run workflow `tool_test_text_manipulation`

-----

## VCF manipulation

```
vcftools_compare and vcftools_subset:
 - doesn't work, but is green when complete
 - not sure if just fileformat of input

vcftools:
 - isec (intersect): works
 - merge: works
 - slice: works
 - annotate: ? ... don't know how to use
```

-----

## FASTA manipulation

Upload `msh2_primer_to_end.fasta`

Run workflow `tool_test_fasta_manipulation`

```
Length distribution
Can't locate GD/Graph/bars.pm in @INC (you may need to install the GD::Graph::bars module) (@INC contains: /etc/perl /usr/local/lib/perl/5.18.2 /usr/local/share/perl/5.18.2 /usr/lib/perl5 /usr/share/perl5 /usr/lib/perl/5.18 /usr/share/perl/5.18 /usr/local/lib/site_perl .) at /mnt/galaxy/tools/fastx_toolkit/0.0.13/devteam/package_fastx_toolkit_0_0_13/ec66ae4c269b/bin/fasta_clipping_histogram.pl line 21.
BEGIN failed--compilation aborted at /mnt/galaxy/tools/fastx_toolkit/0.0.13/devteam/package_fastx_toolkit_0_0_13/ec66ae4c269b/bin/fasta_clipping_histogram.pl line 21.
```

Not tested:
 - Concatenate FASTA alignment by species
 - Barcode Splitter

-----

## BED tools

Upload: `bowtie2_aligned_var_calling.bam` and `var_calling_test.bed`

Set genome as hg19 for both datasets.

-----

## Picard tool

Some tools don't have references... fix below

CollectGcBiasMetrics not working

-----

## Samtools

bcftools view Convert, filter, subset VCF/BCF files
 - don't know if not working or incorrect input


-----

## NGS GATK Tools 2.8

GATK 1.4 not tested

-----

## EMBOSS

Tested transeq, revseq, wordmatch

Most tools not tested

-----

## Blast+

Tested makebalstdb and blastn

-----

## NGS: QC and manipulation


-----

## NGS: Mapping and Assembly

Lastz paired reads / Lastz
 - not tested with no indices


-----

## NGS: RNA Analysis

Not tested:
 - eXpress
 - Filter Combined Transcripts

-----

## NGS: Variant Analysis and Annotation

Not tested:
 - Estimate substitution rates
 - snpFreq
 - Extract reads from a specified region
 - Annotate a VCF file (dbSNP, hapmap)

-----

## Statistics and Graph/Display Data

Count GFF Features
 - Not working
```
Traceback (most recent call last):
  File "/mnt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/devteam/count_gff_features/fabda887a71f/count_gff_features/count_gff_features.py", line 6, in <module>
    from galaxy.datatypes.util.gff_util import GFFReaderWrapper
  File "/mnt/galaxy/galaxy-app/lib/galaxy/datatypes/util/gff_util.py", line 9, in <module>
    from galaxy.util.odict import odict
  File "/mnt/galaxy/galaxy-app/lib/galaxy/util/__init__.py", line 30, in <module>
    from six import binary_type, iteritems, PY3, string_types, text_type
ImportError: No module named six
```
Remove tool

----

Correlation for numeric columns

Scatterplot

```
 Traceback (most recent call last):
   File "/mnt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/devteam/correlation/24e01abf9e34/correlation/cor.py", line 9, in <module>
     from rpy import *
   File "/mnt/galaxy/tools/rpy/1.0.3/devteam/package_rpy_1_0_3/82170c94ca7c/lib/python/rpy.py", line 134, in <module>
     """ % RVERSION)
 RuntimeError: No module named _rpy3030

       RPy module can not be imported. Please check if your rpy
       installation supports R 3.3.0. If you have multiple R versions
       installed, you may need to set RHOME before importing rpy. For
       example:

       >>> from rpy_options import set_options
       >>> set_options(RHOME='c:/progra~1/r/rw2011/')
       >>> from rpy import *
```

Fix:
add
```
source /mnt/galaxy/tools/R/2.11.0/devteam/package_r_2_11_0/5824d2b3bc8b/env.sh
```
into `/mnt/galaxy/tools/rpy/1.0.3/devteam/package_rpy_1_0_3/82170c94ca7c/env.sh`

-----

Summary Statistics
```
Traceback (most recent call last):
  File "/mnt/galaxy/galaxy-app/tools/stats/gsummary.py", line 10, in <module>
    from rpy import BASIC_CONVERSION, NO_CONVERSION, r, RException, set_default_mode
ImportError: No module named rpy
```

Remove Summary Statistics:
From `/mnt/galaxy/galaxy-app/config/tool_conf.xml` delete line
```
106     <tool file="stats/gsummary.xml" />
```
-----

Bar chart
```
Traceback (most recent call last):
  File "/mnt/galaxy/galaxy-app/tools/plotting/bar_chart.py", line 27, in <module>
    import Gnuplot
ImportError: No module named Gnuplot
```

Derek's fix:
```sh
sudo su galaxy
cd /mnt/galaxy/galaxy-app/
source .venv/bin/activate
cd ../tmp
mkdir gnuplot
cd gnuplot/
wget http://sourceforge.net/projects/gnuplot-py/files/latest/download?source=files
mv download\?source=files gnuplot-py-1.8.tar.gz
tar -xzf gnuplot-py-1.8.tar.gz
cd gnuplot-py-1.8/
python setup.py install
```
-----

Hisogram works
T test works

Don't work:
 - Perform LDA
 - Poisson two-sample test

Remove tools

-----

## Filter and Sort, and Join, Subtract and Group

GFF tools not tested

-----

## Convert Formats

BAM to fastq
```
Traceback (most recent call last):
  File "/mnt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/brad-chapman/bam_to_fastq/5a9ada9a3191/bam_to_fastq/bam_to_fastq/bam_to_fastq_wrapper.py", line 48, in <module>
    main(*sys.argv[1:])
  File "/mnt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/brad-chapman/bam_to_fastq/5a9ada9a3191/bam_to_fastq/bam_to_fastq/bam_to_fastq_wrapper.py", line 17, in main
    picard_jar = find_picard_jar("SamToFastq")
  File "/mnt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/brad-chapman/bam_to_fastq/5a9ada9a3191/bam_to_fastq/bam_to_fastq/bam_to_fastq_wrapper.py", line 32, in find_picard_jar
    raise ValueError("Could not find %s in %s" % (name, test_dirs))
ValueError: Could not find SamToFastq in ['/mnt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/brad-chapman/bam_to_fastq/5a9ada9a3191/bam_to_fastq/bam_to_fastq', '/usr/share/java/picard']
```

Remove bam_to_fastq

AXT, LAV, MAF, SFF, bigWig tools not tested

-----

## Operate on Genomic Intervals

Needs bed files with six columns

Works:
 - Concatenate
 - Join
 - Complement
 - Intersect the intervals of two datasets
 - cluster
 - subtract

-----

## Not tested

```
Get Data
Send Data
Lift-Over
Extract Features
Fetch Sequences
Fetch Alignments
```


********************************************************************************

## Indices fix

data_manager_fetch_genome_dbkeys_all_fasta automatically updates to latest version,
even when old revision number is specified.

```bash
sudo su galaxy
cd /mnt/galaxyIndices/tool-data/dm/toolshed.g2.bx.psu.edu/repos/devteam/data_manager_fetch_genome_dbkeys_all_fasta
mv bca4c608408c/ 776bb1b478a0
```

Picard tools:

```
picard tools without indices
less /mnt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/devteam/picard/3a3234d7a2e8/picard/picard_ReorderSam.xml
          <options from_data_table="picard_indexes">


picard tools with indices
less /mnt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/devteam/picard/3a3234d7a2e8/picard/picard_CollectRnaSeqMetrics.xml

               <options from_data_table="all_fasta"></options>


May need to change location of loc files to the ones generated by the data managers in this file

less shed_tool_data_table_conf.xml


Looking at tools which have indices appearing (eg. depth_of_coverage), loc files are empty, but name in
<table comment_char="#" name="gatk_picard_indexes">
        <columns>value, dbkey, name, path</columns>
        <file path="/mnt/galaxyIndices/tool-data/dm/toolshed.g2.bx.psu.edu/repos/devteam/depth_of_coverage/c3f08370fc82/gatk_sorted_picard_index.loc" />
        <tool_shed_repository>
            <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
            <repository_name>depth_of_coverage</repository_name>
            <repository_owner>devteam</repository_owner>
            <installed_changeset_revision>c3f08370fc82</installed_changeset_revision>
            </tool_shed_repository>
    </table>

has another entry for the data manager
<table comment_char="#" name="gatk_picard_indexes">
        <columns>value, dbkey, name, path</columns>
        <file path="/mnt/galaxyIndices/tool-data/dm/toolshed.g2.bx.psu.edu/repos/devteam/data_manager_gatk_picard_index_builder/700f2df51eb0/gatk_sorted_picard_index.loc" />
        <tool_shed_repository>
            <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
            <repository_name>data_manager_gatk_picard_index_builder</repository_name>
            <repository_owner>devteam</repository_owner>
            <installed_changeset_revision>700f2df51eb0</installed_changeset_revision>
            </tool_shed_repository>
    </table>


To make indices appear, add to /mnt/galaxy/var/shed_tool_data_table_conf.xml:

<table comment_char="#" name="picard_indexes">
        <columns>value, dbkey, name, path</columns>
        <file path="/mnt/galaxyIndices/tool-data/dm/toolshed.g2.bx.psu.edu/repos/devteam/data_manager_gatk_picard_index_builder/700f2df51eb0/gatk_sorted_picard_index.loc" />
        <tool_shed_repository>
            <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
            <repository_name>data_manager_gatk_picard_index_builder</repository_name>
            <repository_owner>devteam</repository_owner>
            <installed_changeset_revision>700f2df51eb0</installed_changeset_revision>
            </tool_shed_repository>
    </table>

```

********************************************************************************

## TO DO

Add to Ansible repo:
 - remove bcftools, count_gff_features, lda_analysis, pearson_correlation
 - remove vcftools_compare and vcftools_subset
 - remove bam_to_fastq
 - change `http` to `https` in support link
 - Perl CPAN install GD::Graph::bars module

Manual changes to filesystem
 - remove CollectGcBiasMetrics (picard) (via xml file)
 - remove Summary Statistics (via xml file)
 - `sudo apt-get install oracle-java7-installer` to get gatk to work
 - fix data managers and picard indices

Minor changes:
 - `FASTA manipulation` category should be `FASTX manipulation`
 - Rename `NGS: SAM Tools` to `NGS: SAMtools`
 - Rename `BED tools` to `NGS: BEDtools`
