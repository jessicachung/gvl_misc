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

### Prokka / Update tbl2asn

Download new tbl2asn binary and place in tools.
e.g.
```
/mnt/galaxy/tools/tbl2asn/24.3/takadonet/package_tbl2asn_24_3/41764d6a6a3c/bin
```

-----

## Broken dependency links

e.g. hammock


After installing Hammock, it appears that the address for one of the dependencies, HH-suite, is dead. Fix the link in tool_dependencies.xml:

sudo vi /mnt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/hammock/hammock/4db310b7e37c/hammock/tool_dependencies.xml

Change line 65:
```
<action type="download_by_url">ftp://toolkit.genzentrum.lmu.de/pub/HH-suite/releases/hhsuite-2.0.16-linux-x86_64.tar.gz</action>
```

to

```
<action type="download_by_url">http://wwwuser.gwdg.de/~compbiol/data/hhsuite/releases/all/hhsuite-2.0.16-linux-x86_64.tar.gz</action>
```

In the hammock tool page, uninstall hhsuite, then install it again.


-----

## /tmp disk space issues with Java

Could either create a new destination in job_conf.xml for picard jobs or change a
variable in the picard tool xml.

Change tool instead of creating a new destination:

Edit file:
```
sudo vi /mnt/galaxy/shed_tools/toolshed.g2.bx.psu.edu/repos/devteam/picard/3a3234d7a2e8/picard/picard_macros.xml
```

Change

```
_JAVA_OPTIONS=\${_JAVA_OPTIONS:-'-Xmx2048m -Xms256m'} &amp;&amp;
```

to

```
_JAVA_OPTIONS=\${_JAVA_OPTIONS:-'-Xmx2048m -Xms256m -Djava.io.tmpdir=/mnt/transient_nfs/tmp/'} &amp;&amp;
```

Or could create new destination like so:
```
<destination id="foo" ...>
    <env id="_JAVA_OPTIONS">-Djava.io.tmpdir=/mnt/transient_nfs/tmp/</env>
</destination>
```
But would need to select tools to use destination.

-----

# Adding reference genomes to Galaxy which are not in UCSC

If genome isn't in UCSC:  
ie. not in ftp://hgdownload.cse.ucsc.edu/goldenPath

Find assembly (eg. ASM276v1)  
ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF_000002765.3_ASM276v1

Download to galaxy history  
(Download via galaxy since it automatically unzips the compressed FASTA)  
ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF_000002765.3_ASM276v1/GCF_000002765.3_ASM276v1_genomic.fna.gz

Navigate to:  
Admin  
Local Data  
Run Data Manager Tools  
Create DBKey and Reference Genome  

**Use existing dbkey or create a new one:** New  
**dbkey:** plasFalc1  
**Display name for dbkey:** P. falciparum 3D7 (assembly ASM276v1) (plasFalc1)
**Name of sequence:** *blank*
**ID for sequence:** *blank*
**Choose the source for the reference genome:** History
**FASTA File:** *fasta file*
**Choose the source for the reference genome:** As is

Then run other tools to build indices (eg BWA-MEM index) with Name of sequence:
 *blank* and ID for sequence: *blank*.

-----

# Copy whole tool shed in galaxy-mel to new galaxy-mel-test

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

-----

# Setup mail for Galaxy-Mel

Based on:
https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-postfix-as-a-send-only-smtp-server-on-ubuntu-14-04

Install packages:

```bash
sudo apt-get install postfix mailutils libsasl2-2 ca-certificates libsasl2-modules
```

Select: `Internet Site`  
Mail name:`genome.edu.au`  

Edit config:

```bash
sudo vi /etc/postfix/main.cf
```

Change `inet_interfaces = all` to `inet_interfaces = loopback-only`


Restart postfix:
```bash
sudo service postfix restart
```

Test (replace `your_email_address@gmail.com` with your email):
```bash
echo "Test" | mail -s "Mail test" your_email_address@gmail.com
```

(Note: Mail may go to spam)

Can change host name to change the sent from address.  
http://askubuntu.com/questions/9540/how-do-i-change-the-computer-name


## Enable TLS

http://www.postfix.org/TLS_README.html

```bash
sudo su
dir="$(postconf -h config_directory)"
fqdn=$(postconf -h myhostname)
case $fqdn in /*) fqdn=$(cat "$fqdn");; esac
ymd=$(date +%Y-%m-%d)
key="${dir}/key-${ymd}.pem"
cert="${dir}/cert-${ymd}.pem"
(umask 077; openssl genrsa -out "${key}" 2048) &&
  openssl req -new -key "${key}" \
    -x509 -subj "/CN=${fqdn}" -days 3650 -out "${cert}" &&
  postconf -e \
    "smtpd_tls_cert_file = ${cert}" \
    "smtpd_tls_key_file = ${key}" \
    'smtpd_tls_security_level = may' \
    'smtpd_tls_received_header = yes' \
    'smtpd_tls_loglevel = 1' \
    'smtp_tls_security_level = may' \
    'smtp_tls_loglevel = 1' \
    'smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache' \
    'tls_random_source = dev:/dev/urandom'
exit
```

Restart postfix:
```bash
sudo service postfix restart
```

Test (replace `your_email_address@gmail.com` with your email):
```bash
echo "Test" | mail -s "Mail test TLS" your_email_address@gmail.com
```

## Change galaxy config

Add to `galaxy-app/config/galaxy.ini`:

```
smtp_server = localhost
email_from = Galaxy-Mel <no-reply@galaxy-mel.genome.edu.au>
mailing_join_addr =
```

-----
