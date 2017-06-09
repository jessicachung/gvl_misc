# 4.2 GVL Indices

## Get the blank indices.

After building the filesystem, ssh into the instance. Tar up blank indices
and upload.

```bash
cd /mnt/galaxy_archive
source venv/bin/activate
sudo tar zcf gvl-indices-blank-4.2.0.tar.gz -C /mnt/galaxyIndices/ .
# Enter openstack credentials
swift upload cloudman-gvl-420 gvl-indices-blank-4.2.0.tar.gz --object-name filesystems/gvl-indices-blank-4.2.0.tar.gz
```

## Launch new instance

Edit image to use the new archive when launching.

Launch new instance with blank indices. SSH into the instance.

Download old indices archive. Extract and move genomes into the galaxyIndices
directory.

```bash
# Recommended to do this in a screen session as it takes a while
cd /mnt/transient_nfs
mkdir old_indices && cd old_indices
sudo chown galaxy.galaxy .
sudo su galaxy
# Tutorial indices
wget https://swift.rc.nectar.org.au:8888/v1/AUTH_377/cloudman-gvl-410/filesystems/gvl-indices-tutorial-4.1.0.tar.gz
# Or full indices
# wget https://swift.rc.nectar.org.au:8888/v1/AUTH_377/cloudman-gvl-410/filesystems/gvl-indices-full-4.1.0.tar.gz
tar xzf gvl-indices-tutorial-4.1.0.tar.gz
mv genomes /mnt/galaxyIndices/
exit
```

## Edit new loc files

Add records in old loc files to new loc files.

```bash
sudo su galaxy

OLD_INDICES=/mnt/transient_nfs/old_indices/tool-data/dm/toolshed.g2.bx.psu.edu/repos/devteam/
NEW_INDICES=/mnt/galaxyIndices/tool-data/dm/toolshed.g2.bx.psu.edu/repos/devteam/

grep -v ^# \
    ${OLD_INDICES}/data_manager_fetch_genome_dbkeys_all_fasta/776bb1b478a0/dbkeys.loc \
    >> ${NEW_INDICES}/data_manager_fetch_genome_dbkeys_all_fasta/16ab4dd6a0e5/dbkeys.loc

grep -v ^# \
    ${OLD_INDICES}/data_manager_fetch_genome_dbkeys_all_fasta/776bb1b478a0/all_fasta.loc \
    >> ${NEW_INDICES}/data_manager_fetch_genome_dbkeys_all_fasta/16ab4dd6a0e5/all_fasta.loc

grep -v ^# \
    ${OLD_INDICES}/data_manager_bwa_mem_index_builder/cb0147ade868/bwa_mem_index.loc \
    >> ${NEW_INDICES}/data_manager_bwa_mem_index_builder/46066df8813d/bwa_mem_index.loc

grep -v ^# \
    ${OLD_INDICES}/data_manager_sam_fasta_index_builder/2a1ac1abc3f7/fasta_indexes.loc \
    >> ${NEW_INDICES}/data_manager_sam_fasta_index_builder/1f54e98616af/fasta_indexes.loc

grep -v ^# \
    ${OLD_INDICES}/data_manager_gatk_picard_index_builder/700f2df51eb0/gatk_sorted_picard_index.loc \
    >> ${NEW_INDICES}/data_manager_gatk_picard_index_builder/b31f1fcb203c/gatk_sorted_picard_index.loc

grep -v ^# \
    ${OLD_INDICES}/data_manager_bowtie2_index_builder/9bf4eb559ed5/tophat2_indices.loc \
    >> ${NEW_INDICES}/data_manager_bowtie2_index_builder/83da94c0e4a6/tophat2_indices.loc

grep -v ^# \
    ${OLD_INDICES}/data_manager_bowtie2_index_builder/9bf4eb559ed5/bowtie2_indices.loc \
    >> ${NEW_INDICES}/data_manager_bowtie2_index_builder/83da94c0e4a6/bowtie2_indices.loc

exit
```

Note: delimiters needs to be tabs, not spaces.

## Build HISAT2 indices

Add admin email in cloudman (and auto restart galaxy). Register in galaxy.
Get API key.

```bash
# Enter a screen session
cd /mnt/transient_nfs
git clone https://github.com/gvlproject/gvl.ansible.filesystem.git
cd gvl.ansible.filesystem/files/scripts
source /mnt/galaxy/galaxy-app/.venv/bin/activate
# In the yaml file, comment out all data managers except for hisat2_index_builder_data_manager
./install_genome_indices.py \
    -a <api_key> \
    -i gvl_genome_list_tutorial.yaml 2>&1 \
    | tee build.log
```

Check indices.

## Create archive and upload

Might have to remove the previously downloaded tarball depending on how
much disk space you have.

```bash
sudo mkdir /mnt/galaxy_archive
sudo chown ubuntu.ubuntu /mnt/galaxy_archive
cd /mnt/galaxy_archive
virtualenv venv
source venv/bin/activate
pip install python-swiftclient
pip install python-keystoneclient

sudo tar zcf gvl-indices-tutorial-4.2.0.tar.gz -C /mnt/galaxyIndices/ .

# enter openstack credentials

# 5 GB segment size in bytes
SIZE=$((5*1024*1024*1024))
# 5368709120
swift upload \
    --segment-size ${SIZE} \
    cloudman-gvl-420 \
    gvl-indices-tutorial-4.2.0.tar.gz \
    --object-name filesystems/gvl-indices-tutorial-4.2.0.tar.gz
```

-----

# Missing indices

Some tools have missing indices.

## Fix gatk2 indices

GATK uses the table name: `gatk2_picard_indexes`

Add to `mnt/galaxy/var/shed_tool_data_table_conf.xml`:

```
<table comment_char="#" name="gatk2_picard_indexes">
        <columns>value, dbkey, name, path</columns>
        <file path="/mnt/galaxyIndices/tool-data/dm/toolshed.g2.bx.psu.edu/repos/devteam/data_manager_gatk_picard_index_builder/b31f1fcb203c/gatk_sorted_picard_index.loc" />
        <tool_shed_repository>
            <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
            <repository_name>gatk2</repository_name>
            <repository_owner>iuc</repository_owner>
            <installed_changeset_revision>84584664264c</installed_changeset_revision>
            </tool_shed_repository>
    </table>
```

## Fix picard indices

Picard uses the table name: `picard_indexes`

Add to `mnt/galaxy/var/shed_tool_data_table_conf.xml`:

```
<table comment_char="#" name="picard_indexes">
        <columns>value, dbkey, name, path</columns>
        <file path="/mnt/galaxyIndices/tool-data/dm/toolshed.g2.bx.psu.edu/repos/devteam/data_manager_gatk_picard_index_builder/b31f1fcb203c/gatk_sorted_picard_index.loc" />
        <tool_shed_repository>
            <tool_shed>toolshed.g2.bx.psu.edu</tool_shed>
            <repository_name>picard</repository_name>
            <repository_owner>devteam</repository_owner>
            <installed_changeset_revision>fc288950c3b7</installed_changeset_revision>
            </tool_shed_repository>
    </table>
```
