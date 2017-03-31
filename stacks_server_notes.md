# RAD-Seq Cluster Setup for the Hoffmann Lab
-----

# Launch instance

Launch an instance using the [GVL Launcher](https://launch.genome.edu.au/launch).
More detailed instructions on how to launch a VM can be found
[here](http://vlsci.github.io/lscc_docs/tutorials/gvl_launch/gvl_launch/) if
you are unfamiliar with the process.

Pick an appropriate instance size within your Nectar allocation and choose
persistent volume storage if your account has been granted a storage quota.

Keep the image and flavor as default.

-----

# Add worker nodes (optional)

Once your machine has launched, click on the Cloudman access link on the GVL
dashboard. Access the Cloudman interface by using the password 'ubuntu' and
the password you specified at launch.

Under 'Cluster Controls', click the 'Add worker nodes' button. There you can
specify how many worker nodes and the instance type, then launch the nodes.
Make sure your project has sufficient compute allocation to launch additional
instances.

To remove workers, you can click on the squares representing each worker
instance, and click the power button to terminate the instance.

Going to the 'Admin' page, you can set the master node to run or not run SLURM
jobs by clicking on 'Switch master to not run jobs'.

-----

# SSH into your instance

SSH into the instance as `ubuntu` and the password you specified at launch.

Then install some packages.

```bash
sudo apt-get update
sudo apt-get install libdbd-mysql-perl
```

# Create new users

It is recommended that each user have their own separate account.
Replace `new_user_name` with the username you want to create.

```bash
cd /opt/gvl/gvl_commandline_utilities
sh setup_user.sh new_user_name
# setup password for new user
```

(Ignore warnings)

Append the following line to the end of the new user's .bashrc file
(e.g. `sudo vi /mnt/galaxy/home/new_user_name/.bashrc`):
```
export PATH="/mnt/gvl/anaconda2/bin:$PATH"
```

-----

# Install Stacks

*More detailed instructions can be found in the
[Stacks Manual](http://catchenlab.life.illinois.edu/stacks/manual/#install).*

Download the latest version of Stacks.

```bash
cd ~
mkdir software && cd software
wget http://catchenlab.life.illinois.edu/stacks/source/stacks-1.44.tar.gz
tar xzvf stacks-1.44.tar.gz
cd stacks-1.44
```

Compile and install Stacks.

```bash
./configure
make
sudo make install
```

-----

# Setup Stacks web interface

*TODO: Can this be changed to nginx?*

## Setup Apache and ports

Apache clashes with nginx, so change port to 81.

```bash
sudo vi /etc/apache2/ports.conf
# Change port 80 to 81
```

Then restart apache.

```bash
sudo service apache2 restart
```

## Allow access to port 81

In your internet browser, login to your Nectar dashboard and select your
project under the top toolbar.

Then on the right menu bar, under Compute, click on 'Access & Security'.
Create a new security group by clicking the '+ Create Security Group' button on
the top right.

Give the security group an appropriate name such as "stacks-web". Then click on
the 'Manage Rules' button to add rules.

Click on the '+ Add Rule' button and add port 81, leaving everything else default.

```
Rule: Custom TCP Rule  
Direction: Ingress  
Open Port: Port
Port: 81
Remote: CIDR  
CIDR: 0.0.0.0/0
```

Then add it to the instance you just launched:

On the right menu bar, under Compute, click on 'Instances'. Find your instance
and click the dropdown button on the right-most column. Then click 'Edit
Security Groups' and click the '+' button next to the security group you just
created to apply it to the instance.

Test by seeing if you can access `<IP_address>`:81 in your browser.

## Install dependencies

```bash
sudo apt-get install mysql-server mysql-client
# Enter password for the MySQL root user or leave blank
sudo apt-get install php5 php5-mysqlnd
sudo apt-get install libspreadsheet-writeexcel-perl
```

## Configure MySQL

Copy MySQL configuration file.

```bash
cd /usr/local/share/stacks/sql/
sudo cp mysql.cnf.dist mysql.cnf
```

Run MySQL as root.

```bash
sudo mysql

## Or if you set a password then
# sudo mysql -p
```

In MySQL, add user `ubuntu` with a password e.g. (`ubuntu_stacks`) to MySQL.

```sql
GRANT ALL ON *.* TO 'ubuntu'@'localhost' IDENTIFIED BY 'ubuntu_stacks';
```

(Optional) Grant permissions to everyone

```sql
GRANT ALL ON *.* TO ''@'localhost' IDENTIFIED BY '';
exit
```

Note:
```
# To revoke privileges
REVOKE ALL PRIVILEGES ON *.* FROM ''@'localhost';

# Check privileges
select * from information_schema.user_privileges;

# Total reset if you screw up something:
sudo apt-get purge mysql-server mysql-client mysql-common mysql-server-core-5.5 mysql-client-core-5.5
sudo rm -rf /etc/mysql /var/lib/mysql
sudo apt-get autoremove
sudo apt-get autoclean

# View error log
less /var/log/mysql/error.log
```

Change config for stacks MySQL access.

```bash
sudo vi /usr/local/share/stacks/sql/mysql.cnf
```

And replace the user and password. eg:
```
user=ubuntu
password=ubuntu_stacks
```


## Change location of the MySQL database (optional)

The amount of primary disk storage space is limited, so we need to move
the mysql directory to transient or volume storage.

```bash
sudo service mysql stop
sudo mkdir /mnt/transient_nfs/mysql_database/
# Or if you have volumne storage, use /mnt/galaxy/mysql_database
sudo cp -rap /var/lib/mysql /mnt/transient_nfs/mysql_database/
sudo chown mysql.mysql /mnt/transient_nfs/mysql_database
```

Then change the location in the config:

```bash
sudo vi /etc/mysql/my.cnf
```

And change:
```
datadir     = /var/lib/mysql
```
to
```
datadir     = /mnt/transient_nfs/mysql_database/mysql
```

Edit Apparmor config:
```bash
sudo vi /etc/apparmor.d/usr.sbin.mysqld
```
Change two lines:
```
/var/lib/mysql/ r,                                                               
/var/lib/mysql/** rwk,
```
to
```
/mnt/transient_nfs/mysql_database/mysql/ r,
/mnt/transient_nfs/mysql_database/mysql/** rwk,
```

Reload apparmor and start mysql.

```bash
sudo service apparmor reload
sudo service mysql start
```

## Configure Apache

Create a new conf file for Apache.

```bash
sudo vi /etc/apache2/conf-available/stacks.conf
```

Add the following lines to the new file.

```
<Directory "/usr/local/share/stacks/php/">
    Order deny,allow
    Deny from all
    Allow from all
	Require all granted
</Directory>

Alias / "/usr/local/share/stacks/php/"
```

Create a symlink to the conf file in the `conf-enabled` directory.

```bash
cd /etc/apache2/conf-enabled
sudo ln -s ../conf-available/stacks.conf .
```

Restart apache.

```bash
sudo service apache2 restart
```
Check `<IP_address>`:81 which should have the Stacks interface.

## Configure nginx

Add nginx location.

```bash
cd /etc/nginx/sites-enabled/
sudo vi stacks.locations
```

Add to `stacks.locations`:

```
location /stacks/ {
    rewrite ^/stacks/(.*) /$1 break;
    proxy_pass http://localhost:81;
    proxy_set_header   X-Forwarded-Host $host;
    proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
}
```

Restart nginx.

```bash
sudo service nginx restart
```

Check `<IP_address>`/stacks/ which should have the Stacks interface.


## Configure database access

Edit the PHP config file to allow access to MySQL using the `ubuntu` user and
password.

```bash
sudo cp /usr/local/share/stacks/php/constants.php.dist /usr/local/share/stacks/php/constants.php
sudo vi /usr/local/share/stacks/php/constants.php
```

Set `$db_user` and `$db_pass` variables to `ubuntu` and `ubuntu_stacks`.

-----

## Enable web-based exporting from the MySQL database (optional)

```bash
sudo chown www-data /usr/local/share/stacks/php/export
```

### Setup email

Specify the email and SMTP server to use in notification messages if necessary.


Install packages:

```bash
sudo apt-get install postfix mailutils libsasl2-2 ca-certificates libsasl2-modules
```

Select: `Internet Site`
Mail name:`stacks-mail-server.test`

Edit config:
```bash
sudo vi /etc/postfix/main.cf
```
Change `inet_interfaces = all` to `inet_interfaces = loopback-only`


Restart postfix:
```bash
sudo service postfix restart
```

Test:
```bash
echo "Test" | mail -s "Mail test" your_email_address@gmail.com
```

Can change host name to change the sent from address.  
http://askubuntu.com/questions/9540/how-do-i-change-the-computer-name


### Enable TLS

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

Test:
```bash
echo "Test" | mail -s "Mail test TLS" your_email_address@gmail.com
```

Edit export settings:

```bash
sudo vi /usr/local/bin/stacks_export_notify.pl
```

Change `$url` to:

```
my $url           = "http://115.146.85.115/stacks/export/";
```

Test Stacks export through the web interface.
Note that the email may be filtered as spam.

-----

# Install software

Use bioconda to install software.

## Install Anaconda

Download and install Anaconda.

```bash
wget https://repo.continuum.io/archive/Anaconda2-4.2.0-Linux-x86_64.sh
bash Anaconda2-4.2.0-Linux-x86_64.sh
# Accept license
# Set directory as:
/mnt/gvl/anaconda2
# Append to bashrc
# Refresh bashrc
source ~/.bashrc

# Change ownership of directory
sudo chown -R ubuntu:ubuntu /mnt/gvl/anaconda2
```

Setup channels

```bash
conda config --add channels conda-forge
conda config --add channels defaults
conda config --add channels r
conda config --add channels bioconda
```

## Install software using conda

```bash
conda install fastqc
conda install trimmomatic
conda install bwa=0.7.15
conda install snap-aligner=1.0beta.18
conda install samtools=1.3.1
conda install vcftools=0.1.14
conda install plink2=1.90b3.35
conda install parallel

# openmpi is already installed but v1.8.5
# conda install openmpi
```


The Stacks script which connects to MySQL requires modules in perl that aren't
installed with the anaconda perl. Use `/usr/bin/perl` instead.

```bash
sudo vi /usr/local/bin/index_radtags.pl
# Change the first line to:
# #!/usr/bin/perl
```

## Install Bowtie

Conda may not have the latest versions of bowtie. Check http://bioconda.github.io/
before installing.

```bash
conda install bowtie=1.2.0
conda install bowtie2=2.3.0
```

Or download and extract archives then compile from source.

```bash
wget https://sourceforge.net/projects/bowtie-bio/files/bowtie2/2.3.0/bowtie2-2.3.0-source.zip/download
unzip download
rm download
wget https://sourceforge.net/projects/bowtie-bio/files/bowtie/1.2.0/bowtie-1.2-src.zip/download
unzip download
rm download
```

Compile and install bowtie.

```bash
sudo apt-get install libtbb-dev
cd bowtie2-2.3.0
make
sudo make install
cd ../bowtie-1.2
make
sudo make install
```

## Manually install GATK and IGV

Download the software from the Broad Institute and transfer it to the instance.

```bash
cd /mnt/gvl/apps
# Jess' personal copies in object store:
# sudo wget https://swift.rc.nectar.org.au:8888/v1/AUTH_377/jchung/IGV_2.3.89.zip
# sudo wget https://swift.rc.nectar.org.au:8888/v1/AUTH_377/jchung/GenomeAnalysisTK-3.7.tar.bz2
```

Extract the archives.

```bash
sudo unzip IGV_2.3.89.zip
sudo mkdir GenomeAnalysisTK-3.7
sudo tar -xjvf GenomeAnalysisTK-3.7.tar.bz2 --directory GenomeAnalysisTK-3.7
# Clean up archives
sudo rm IGV_2.3.89.zip GenomeAnalysisTK-3.7.tar.bz2
```

## Install Mediaflux

Download [Mediaflux](http://nsp.nectar.org.au/resplat-wiki/doku.php?id=data_management:mediaflux:downloads)
and transfer it to the instance.

Make directory and move jar archive.

```bash
mkdir /mnt/gvl/apps/Mediaflux-1.0.5
```

## (Optional) Create wrapper scripts

```bash
cd ~/Desktop
```

Mediaflux wrapper (`vi mediaflux.sh`):
```
#!/bin/bash

/usr/bin/java -jar /mnt/gvl/apps/Mediaflux-1.0.5/mexplorer-1.0.5.jar
```

IGV wrapper (`vi igv.sh`):
```
#!/bin/bash

/usr/bin/java \
    -Xmx4g \
    -jar /mnt/gvl/apps/IGV_2.3.89/igv.jar
```

Make executable.
```
chmod +x *.sh
```

Test if working by logging on with VNC.

Copy to the Desktop directory of existing users.

-----

# Optional aesthetic stuff

## Add Stacks to the dashboard

Edit:
```
sudo vi /opt/gvl/gvldash/gvldash/service_registry.yml
```

And add:
```
- access_instructions: null
  description: 'The Stacks web interface.

    '
  display_name: Stacks
  installation_path: /usr/sbin/sshd
  logo: /static/images/logo_public_html.png
  name: stacks
  process_name: nginx
  type: web
  virtual_path: /stacks
```
