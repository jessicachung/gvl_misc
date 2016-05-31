# Missing Galaxy tools (GVL 4.1)

Tools marked as installed, but when viewing repository details, some tools are
marked as "Invalid tools". These invalid tools don't appear in the tool panel.

## Step 1

From the SQL database in `/mnt/galaxy/var`, get a list of invalid tools:

```sql
ATTACH "galaxy_install_db.sqlite" as db1;
.mode tabs
.output invalid.tsv
select * from db1.tool_shed_repository where metadata like '%invalid_tools%';
.output stdout
```

List of tool repos with invalid tools with `less invalid.tsv | cut -f 5`:
```
picard
samtools_calmd
samtools_stats
sam_to_bam
samtools_mpileup
sam_pileup
cufflinks
cuffdiff
cuffquant
cuffmerge
cuffcompare
freebayes
```

List of invalid tools with
`less invalid.tsv | cut -f 10 | cut -d "]" -f 1 | tr '"' '\n' | grep "xml$"`:
```
picard_CollectWgsMetrics.xml
picard_CollectRnaSeqMetrics.xml
picard_QualityScoreDistribution.xml
picard_CollectBaseDistributionByCycle.xml
picard_CollectAlignmentSummaryMetrics.xml
picard_CollectGcBiasMetrics.xml
picard_CollectInsertSizeMetrics.xml
picard_MeanQualityByCycle.xml
samtools_calmd.xml
samtools_stats.xml
sam_to_bam.xml
samtools_mpileup.xml
sam_pileup.xml
cufflinks_wrapper.xml
cuffdiff_wrapper.xml
cuffquant_wrapper.xml
cuffmerge_wrapper.xml
cuffcompare_wrapper.xml
freebayes.xml
leftalign.xml
```


## Step 2

In Galaxy, update metadata in all tools using the 'Reset metadata' link in
the admin section and selecting all repositories.

This takes a few minutes. Once complete, the invalid tools should be valid, but they don't appear in the tool panel.


## Step 3

Add the new tools to the toolshed configuration XML file.


```bash
cd /mnt/galaxy/galaxy-app/config/

# Backup old tool configuration
mv shed_tool_conf_cloud.xml backup_shed_tool_conf_cloud.xml

# Remove blank lines
less backup_shed_tool_conf_cloud.xml | sed '/^$/d' > shed_tool_conf_cloud.xml

# Add xml at the end, just before the closing of the toolbox tag
vi shed_tool_conf_cloud.xml
```


```xml
<section id="ngs_picard" name="NGS: Picard" version="">
    <tool file="toolshed.g2.bx.psu.edu/repos/devteam/picard/efc56ee1ade4/picard/picard_CollectWgsMetrics.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_CollectWgsMetrics/1.136.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>picard</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>efc56ee1ade4</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_CollectWgsMetrics/1.136.0</id>
        <version>1.136.0</version>
    </tool>

    <tool file="toolshed.g2.bx.psu.edu/repos/devteam/picard/efc56ee1ade4/picard/picard_CollectRnaSeqMetrics.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_CollectRnaSeqMetrics/1.136.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>picard</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>efc56ee1ade4</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_CollectRnaSeqMetrics/1.136.0</id>
        <version>1.136.0</version>
    </tool>

    <tool file="toolshed.g2.bx.psu.edu/repos/devteam/picard/efc56ee1ade4/picard/picard_QualityScoreDistribution.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_QualityScoreDistribution/1.136.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>picard</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>efc56ee1ade4</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_QualityScoreDistribution/1.136.0</id>
        <version>1.136.0</version>
    </tool>

    <tool file="toolshed.g2.bx.psu.edu/repos/devteam/picard/efc56ee1ade4/picard/picard_CollectBaseDistributionByCycle.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_Colle
ctBaseDistributionByCycle/1.136.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>picard</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>efc56ee1ade4</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_CollectBaseDistributionByCycle/1.136.0</id>
        <version>1.136.0</version>
    </tool>

    <tool file="toolshed.g2.bx.psu.edu/repos/devteam/picard/efc56ee1ade4/picard/picard_CollectAlignmentSummaryMetrics.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_CASM/
1.136.1">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>picard</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>efc56ee1ade4</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_CASM/1.136.1</id>
        <version>1.136.1</version>
    </tool>

    <tool file="toolshed.g2.bx.psu.edu/repos/devteam/picard/efc56ee1ade4/picard/picard_CollectGcBiasMetrics.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_CollectGcBiasMe
trics/1.136.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>picard</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>efc56ee1ade4</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_CollectGcBiasMetrics/1.136.0</id>
        <version>1.136.0</version>
    </tool>

    <tool file="toolshed.g2.bx.psu.edu/repos/devteam/picard/efc56ee1ade4/picard/picard_CollectInsertSizeMetrics.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_CollectInsertSizeMetrics/1.136.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>picard</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>efc56ee1ade4</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_CollectInsertSizeMetrics/1.136.0</id>
        <version>1.136.0</version>
    </tool>

    <tool file="toolshed.g2.bx.psu.edu/repos/devteam/picard/efc56ee1ade4/picard/picard_MeanQualityByCycle.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_MeanQualityByCycl
e/1.136.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>picard</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>efc56ee1ade4</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/picard/picard_MeanQualityByCycle/1.136.0</id>
        <version>1.136.0</version>
    </tool>
</section>

<section id="samtools" name="NGS: SAM Tools" version="">
  <tool file="toolshed.g2.bx.psu.edu/repos/devteam/samtools_calmd/1ebb4ecdc1ef/samtools_calmd/samtools_calmd.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/samtools_calmd/samtools_calmd/2.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>samtools_calmd</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>1ebb4ecdc1ef</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/samtools_calmd/samtools_calmd/2.0</id>
        <version>2.0</version>
    </tool>
</section>

<section id="samtools" name="NGS: SAM Tools" version="">
  <tool file="toolshed.g2.bx.psu.edu/repos/devteam/samtools_stats/8cfc17e27132/samtools_stats/samtools_stats.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/samtools_stats/samtools_stats/2.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>samtools_stats</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>8cfc17e27132</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/samtools_stats/samtools_stats/2.0</id>
        <version>2.0</version>
    </tool>
</section>

<section id="samtools" name="NGS: SAM Tools" version="">
  <tool file="toolshed.g2.bx.psu.edu/repos/devteam/sam_to_bam/881e16ad05c6/sam_to_bam/sam_to_bam.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/sam_to_bam/sam_to_bam/2.1">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>sam_to_bam</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>881e16ad05c6</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/sam_to_bam/sam_to_bam/2.1</id>
        <version>2.1</version>
    </tool>
</section>

<section id="samtools" name="NGS: SAM Tools" version="">
  <tool file="toolshed.g2.bx.psu.edu/repos/devteam/samtools_mpileup/bfc4517aa037/samtools_mpileup/samtools_mpileup.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/samtools_mpileup/samtools_mpileup/2.1.1">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>samtools_mpileup</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>bfc4517aa037</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/samtools_mpileup/samtools_mpileup/2.1.1</id>
        <version>2.1.1</version>
    </tool>
</section>

<section id="samtools" name="NGS: SAM Tools" version="">
  <tool file="toolshed.g2.bx.psu.edu/repos/devteam/sam_pileup/890d97772e2a/sam_pileup/sam_pileup.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/sam_pileup/sam_pileup/1.1.2">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>sam_pileup</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>890d97772e2a</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/sam_pileup/sam_pileup/1.1.2</id>
        <version>1.1.2</version>
    </tool>
</section>

<section id="ngs_rna_tools" name="NGS: RNA Analysis" version="">
  <tool file="toolshed.g2.bx.psu.edu/repos/devteam/cufflinks/a1ea9af8d5f4/cufflinks/cufflinks_wrapper.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/cufflinks/cufflinks/2.2.1.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>cufflinks</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>a1ea9af8d5f4</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/cufflinks/cufflinks/2.2.1.0</id>
        <version>2.2.1.0</version>
    </tool>
</section>

<section id="ngs_rna_tools" name="NGS: RNA Analysis" version="">
  <tool file="toolshed.g2.bx.psu.edu/repos/devteam/cuffdiff/0f32ad418df8/cuffdiff/cuffdiff_wrapper.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/cuffdiff/cuffdiff/2.2.1.3">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>cuffdiff</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>0f32ad418df8</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/cuffdiff/cuffdiff/2.2.1.3</id>
        <version>2.2.1.3</version>
    </tool>
</section>

<section id="ngs_rna_tools" name="NGS: RNA Analysis" version="">
  <tool file="toolshed.g2.bx.psu.edu/repos/devteam/cuffquant/986b63735a5e/cuffquant/cuffquant_wrapper.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/cuffquant/cuffquant/2.2.1.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>cuffquant</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>986b63735a5e</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/cuffquant/cuffquant/2.2.1.0</id>
        <version>2.2.1.0</version>
    </tool>
</section>

<section id="ngs_rna_tools" name="NGS: RNA Analysis" version="">
  <tool file="toolshed.g2.bx.psu.edu/repos/devteam/cuffmerge/8160c8ea4eb9/cuffmerge/cuffmerge_wrapper.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/cuffmerge/cuffmerge/2.2.1.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>cuffmerge</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>8160c8ea4eb9</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/cuffmerge/cuffmerge/2.2.1.0</id>
        <version>2.2.1.0</version>
    </tool>
</section>

<section id="ngs_rna_tools" name="NGS: RNA Analysis" version="">
  <tool file="toolshed.g2.bx.psu.edu/repos/devteam/cuffcompare/b77178f66fc3/cuffcompare/cuffcompare_wrapper.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/cuffcompare/cuffcompare/2.2.1.0">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>cuffcompare</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>b77178f66fc3</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/cuffcompare/cuffcompare/2.2.1.0</id>
        <version>2.2.1.0</version>
    </tool>
</section>

<section id="ngs_variant_analysis" name="NGS: Variant Analysis" version="">
  <tool file="toolshed.g2.bx.psu.edu/repos/devteam/freebayes/99684adf84de/freebayes/freebayes.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/freebayes/freebayes/0.4.1">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>freebayes</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>99684adf84de</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/freebayes/freebayes/0.4.1</id>
        <version>0.4.1</version>
    </tool>
    <tool file="toolshed.g2.bx.psu.edu/repos/devteam/freebayes/99684adf84de/freebayes/leftalign.xml" guid="toolshed.g2.bx.psu.edu/repos/devteam/freebayes/bamleftalign/0.4">
      <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
        <repository_name>freebayes</repository_name>
        <repository_owner>devteam</repository_owner>
        <installed_changeset_revision>99684adf84de</installed_changeset_revision>
        <id>toolshed.g2.bx.psu.edu/repos/devteam/freebayes/bamleftalign/0.4</id>
        <version>0.4</version>
    </tool>
</section>


```


## Step 4

Reload the tool panel
In the Galaxy admin page, go to 'Reload a tool's configuration' and click the
option to reload the whole toolbox. This will take about a minute to refresh the
database and build a new `integrated_tool_panel.xml`.
