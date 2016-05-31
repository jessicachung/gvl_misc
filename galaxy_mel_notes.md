# Galaxy-Mel notes

## Update Samtools

Old version of Samtools can cause the whole Galaxy server to crash in GVL 4.0.

To fix, update the version of samtools in environment.

```bash
wget https://github.com/samtools/samtools/releases/download/1.3.1/samtools-1.3.1.tar.bz2

tar xvjf samtools-1.3.1.tar.bz2
cd samtools-1.3.1

./configure
make
sudo mv samtools /usr/bin/
```

Don't need to add in worker nodes.

-----
## Installing tools

### package_r_3_2_1

Doesn't install correctly on NFS (permissions/owner issue)  
/mnt/galaxy/tools/R/3.2.1/iuc/package_r_3_2_1/d0bf97420fb5

Change `tool_dependencies.xml` in
`/mnt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/iuc/package_r_3_2_1/d0bf97420fb5/package_r_3_2_1/`
Change `cp -p` to `cp`

-----
### Metagenomics tools

Need to copy usearch to all worker nodes, chmod +x, and mv to /usr/local/bin

-----
### Tabix dependency for vcf tools

Need to install on all worker nodes as well as the head node

`sudo apt-get install tabix`

-----
### RAD-seq tools

Add:  
`<tool_shed name="Galaxy GenOuest tool shed" url="http://toolshed.genouest.org/"/>`
to `/mnt/galaxy/galaxy-app/config/tool_sheds_conf.xml`

Install 'stacks'

Add to new panel section  
NGS: RAD-Seq

-----
### Prokka / Update tbl2asn

Download new tbl2asn binary and place in tools.
e.g.
/mnt/galaxy/tools/tbl2asn/24.3/takadonet/package_tbl2asn_24_3/41764d6a6a3c/bin





-----
## Copy whole tool shed in galaxy-mel to new galaxy-mel-test

```bash
# SSH into galaxy-mel-test
ssh ubuntu@45.113.234.185

# Stop galaxy in cloudman

mv var shed_tools del
mv galaxy-app/config galaxy-app/static tools del

# Rsync from galaxy-mel to galaxy-mel-test
rsync -av galaxy@45.113.232.91:/mnt/galaxy/var .
rsync -av galaxy@45.113.232.91:/mnt/galaxy/shed_tools .
rsync -av galaxy@45.113.232.91:/mnt/galaxy/tools .
rsync -av galaxy@45.113.232.91:/mnt/galaxy/galaxy-app/config galaxy-app
rsync -av galaxy@45.113.232.91:/mnt/galaxy/galaxy-app/static galaxy-app

# change background in welcome.html

# Approx size
# ubuntu@45.113.232.91:/mnt/galaxy $ du -hs tools
# du: cannot read directory ... Permission denied
# 8.3G	tools
# ubuntu@45.113.232.91:/mnt/galaxy $ du -sh shed_tools/
# 154M	shed_tools/
```
