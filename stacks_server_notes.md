# RAD-Seq Cluster Setup for the Hoffmann Lab

This documentation assumes you want a cluster consisting of multiple VMs.
If you only have one VM, you can leave Stacks to use the default MySQL
settings (localhost) instead of setting up remote access.

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
sudo apt-get install libdbd-mysql-perl moshe speedometer
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

-----

# Install Stacks

*More detailed instructions can be found in the
[Stacks Manual](http://catchenlab.life.illinois.edu/stacks/manual/#install).*

Download the latest version of Stacks.

```bash
cd ~
mkdir software && cd software
wget http://catchenlab.life.illinois.edu/stacks/source/stacks-1.47.tar.gz
tar xzvf stacks-1.47.tar.gz
cd stacks-1.47
```

Compile and install Stacks on the volume.

```bash
sudo mkdir /mnt/galaxy/gvl/software
sudo chown ubuntu.ubuntu /mnt/galaxy/gvl/software
mkdir /mnt/galaxy/gvl/software/stacks-1.47
./configure --prefix=/mnt/galaxy/gvl/software/stacks-1.47/
make
make install
```

Create a symlink for the latest version of stacks. This makes things easier if we want
to update Stacks in the future.

```
ln -s stacks-1.47 /mnt/galaxy/gvl/software/stacks
```

If installing later versions of Stacks on Ubuntu 14.04, the default compiler won't work.
You can install gcc6 with:

```bash
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get install gcc-6
sudo apt-get install g++-6
export CXX=g++-6
# Then configure, make, and make install like above
```

Make a copy of the config file the stacks web server.

```bash
cp /mnt/galaxy/gvl/software/stacks/share/stacks/php/constants.php.dist /mnt/galaxy/gvl/software/stacks/share/stacks/php/constants.php
```

-----

# Setup Stacks web interface

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

Repeat the above '+ Add Rule' step to add port 9996 for MySQL.

Then add the security group to the instance you just launched:

On the right menu bar, under Compute, click on 'Instances'. Find your instance
and click the dropdown button on the right-most column. Then click 'Edit
Security Groups' and click the '+' button next to the security group you just
created to apply it to the instance.

Test by seeing if you can access `<IP_address>:81/html` in your browser. You
should see a html page with the heading "Apache2 Ubuntu Default Page".


## Install dependencies

```bash
sudo apt-get install mysql-server mysql-client
# Enter password for the MySQL root user or leave blank
sudo apt-get install php5 php5-mysqlnd
sudo apt-get install libspreadsheet-writeexcel-perl

# On ubuntu 16.04, only php7 is available
sudo apt-get install libapache2-mod-php php7.0-mysql
```

## Configure MySQL

Copy MySQL configuration file.

```bash
cd /mnt/galaxy/gvl/software/stacks/share/stacks/sql/
cp mysql.cnf.dist mysql.cnf
```

Run MySQL as root.

```bash
sudo mysql

## Or if you set a password then
# sudo mysql -p
```

In MySQL, add user `ubuntu` with a password e.g. (`my_password`) to MySQL.
Make the password secure if you plan to access the database remotely with your
worker nodes.

```sql
GRANT ALL ON *.* TO 'ubuntu'@'localhost' IDENTIFIED BY 'my_password';
```

Allow remote access with:

```sql
GRANT ALL ON *.* TO 'ubuntu'@'%' IDENTIFIED BY 'my_password';
```

(Optional) Grant permissions to users at localhost.

```sql
CREATE USER 'jess'@'localhost' IDENTIFIED BY '';
GRANT ALL ON *.* TO 'jess'@'localhost';
exit
```


Note:
```
# View users
SELECT host, user FROM mysql.user;

# To change password
SET PASSWORD FOR 'ubuntu'@'localhost' = PASSWORD('my_new_password');

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
vi /mnt/galaxy/gvl/software/stacks/share/stacks/sql/mysql.cnf
```

And replace the user, password, host (where `XXX.XXX.XXX.XXX` is your IP
address), and port. eg:

```
[client]
user=ubuntu
password=my_password
host=XXX.XXX.XXX.XXX
port=9996
local-infile=1
```

## Change location of the MySQL database and configure for remote access (optional)

The amount of primary disk storage space is limited, so we need to move
the mysql directory to transient or volume storage.

```bash
sudo service mysql stop
sudo mkdir /mnt/galaxy/mysql_database
sudo cp -rap /var/lib/mysql /mnt/galaxy/mysql_database
sudo chown mysql.mysql /mnt/galaxy/mysql_database
```

Then change the location in the config:

```bash
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
# For ubuntu 14.04
# sudo vi /etc/mysql/my.cnf
```

And change:
```
datadir     = /var/lib/mysql
```
to
```
datadir     = /mnt/galaxy/mysql_database/mysql
```

Also configure MySQL for remote access by changing the following under the 
`[mysqld]` heading:

 - Change port to `9996`
 - change bind address from `127.0.0.1` to `0.0.0.0`


Edit Apparmor config:
```bash
sudo vi /etc/apparmor.d/usr.sbin.mysqld
```
Change two lines under `# Allow data dir access`:
```
  /var/lib/mysql/ r,                                                               
  /var/lib/mysql/** rwk,
```
to
```
  /mnt/galaxy/mysql_database/mysql/ r,
  /mnt/galaxy/mysql_database/mysql/** rwk,
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
<Directory "/mnt/galaxy/gvl/software/stacks/share/stacks/php/">
    Order deny,allow
    Deny from all
    Allow from all
	Require all granted
</Directory>

Alias / "/mnt/galaxy/gvl/software/stacks/share/stacks/php/"
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


## Configure database access for the Stacks web interface

Edit the PHP config file to allow access to MySQL using the `ubuntu` user and
password.

```bash
vi /mnt/galaxy/gvl/software/stacks/share/stacks/php/constants.php
```

Set `$db_user` and `$db_pass` variables to `ubuntu` and `my_password`. 
Leave `$db_host` as `localhost`.

-----


## Configure worker nodes

For each worker node, login and install the MySQL client.

```bash
sudo apt-get update
sudo apt-get install mysql-client
```

Test you can access the MySQL database on the head node while you're logged in
the worker node. Replace `XXX.XXX.XXX.XXX` with the IP of your head node.

```bash
mysql -u ubuntu -p -h XXX.XXX.XXX.XXX -P 9996
```

-----

## Enable web-based exporting from the MySQL database (optional)

```bash
sudo chown www-data /mnt/galaxy/gvl/software/stacks/share/stacks/php/export
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
sudo vi /mnt/galaxy/gvl/software/stacks/bin/stacks_export_notify.pl
```

Change `$url` (and replace `XXX.XXX.XXX.XXX` with your IP) to:

```
my $url           = "http://XXX.XXX.XXX.XXX/stacks/export/";
```

Test Stacks export through the web interface.
Note that the email may be filtered as spam.

-----

# Install software

Use bioconda to install software.

## Install Anaconda

Download and install Anaconda.

```bash
wget https://repo.continuum.io/archive/Anaconda2-4.4.0-Linux-x86_64.sh
sudo bash Anaconda2-4.4.0-Linux-x86_64.sh
# Accept license
# Set directory as:
/mnt/galaxy/gvl/anaconda2
# Append to bashrc
# Refresh bashrc
source ~/.bashrc

# Change ownership of directory
sudo chown -R ubuntu:ubuntu /mnt/galaxy/gvl/anaconda2
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
conda install plink2
conda install parallel

# openmpi is already installed but v1.8.5
# conda install openmpi
```

## Fix Stacks Perl

Some Stacks Perl scripts require DBI which `/usr/bin/perl` can access, but not the
Anaconda Perl. Change the shebang from

```
#!/usr/bin/env perl
```

to

```
#!/usr/bin/perl
```

for the following scripts:

```
/mnt/galaxy/gvl/software/stacks/bin/export_sql.pl
/mnt/galaxy/gvl/software/stacks/bin/index_radtags.pl
/mnt/galaxy/gvl/software/stacks/bin/load_sequences.pl
```

## Install Bowtie

Conda may not have the latest versions of bowtie. Check http://bioconda.github.io/
before installing.

```bash
conda install bowtie
conda install bowtie2
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
cd /mnt/galaxy/gvl/software/
# Jess' personal copies in object store:
# wget https://swift.rc.nectar.org.au:8888/v1/AUTH_377/jchung/IGV_2.3.89.zip
# wget https://swift.rc.nectar.org.au:8888/v1/AUTH_377/jchung/GenomeAnalysisTK-3.7.tar.bz2
```

Extract the archives.

```bash
unzip IGV_2.3.89.zip
mkdir GenomeAnalysisTK-3.7
tar -xjvf GenomeAnalysisTK-3.7.tar.bz2 --directory GenomeAnalysisTK-3.7
# Clean up archives
rm IGV_2.3.89.zip GenomeAnalysisTK-3.7.tar.bz2
```

## Install Mediaflux Explorer

[Mediaflux](https://vicnode.org.au/access/mediaflux/) will be used to interact with
VicNode storage. Mediaflux Explorer is a Java application that can be used with VNC as a
graphical interface.

Make directory and download jar archive.

```bash
mkdir /mnt/galaxy/gvl/software/Mediaflux && cd /mnt/galaxy/gvl/software/Mediaflux
wget https://wiki.cloud.unimelb.edu.au/resplat/lib/exe/fetch.php?media=wiki:public:mediaflux:mexplorer-1.3.9.jar
```

Rename the archive.

## (Optional) Create wrapper scripts

```bash
cd ~/Desktop
```

Mediaflux wrapper (`vi mediaflux.sh`):
```
#!/bin/bash

/usr/bin/java -jar /mnt/galaxy/gvl/software/Mediaflux/mexplorer-1.3.9.jar
```

IGV wrapper (`vi igv.sh`):
```
#!/bin/bash

/usr/bin/java \
    -Xmx4g \
    -jar /mnt/galaxy/gvl/software/IGV_2.3.89/igv.jar
```

Make executable.
```
chmod +x *.sh
```

Test if working by logging on with VNC.

Copy to the Desktop directory of existing users.

-----

# Edit PATH

Append the following to `PATH` in `/etc/environment` at the beginning.

```
/mnt/galaxy/gvl/software/stacks/bin:/mnt/galaxy/gvl/anaconda2/bin:
```

Or alternatively, add to the end of each user's `.bashrc` file:

```
export PATH="/mnt/galaxy/gvl/stacks/bin:/mnt/gvl/anaconda2/bin:$PATH"
```
-----

# Reboot

If cluster reboots, you'll need to start apache and mysql again:

```bash
sudo service apache2 start
sudo service mysql start
```

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

## SLURM accounting

Note that adding the accounting config to `/mnt/transient_nfs/slurm/slurm.conf`
directly won't create the accounting file and won't work in GVL 4.2.

Interfacing with slurm via `service` no longer works. Need to start/stop via 
cloudman in GVL 4.2.

Add accounting storage config to the Cloudman template.

```
sudo vi /mnt/cm/cm/conftemplates/slurm.conf.default
```

Add the below lines to the logging section (e.g. under `JobCompType`)

```
AccountingStorageType=accounting_storage/filetxt
AccountingStorageLoc=/var/log/slurm-llnl/accounting
```

Go to the CloudMan interface and restart slurmd and slurmctld.

Check with `sacct`.

The accounting file needs to be readable. Change it with 
```
sudo chmod a+r /var/log/slurm-llnl/accounting
```

Each time the VM does a reboot, cloudman is re-downloaded from the container so
you may need to change the default template again.


## SLURM custom wrappers

For ease of use, add wrapper scripts.

`/usr/local/bin/squeue-custom`:

```
#!/bin/bash
# Wrapper for squeue for larger job column width and CPU column
squeue -o "%.6i %.20j %.8u %.8T %.6M %.9l %.6C %.6D %R"
```

`/usr/local/bin/sinteractive`:

```
#!/bin/bash
# Wrapper for interactive jobs copied from VLSCI clusters
exec srun $* --pty -u ${SHELL} -i -l
```

## Change hostname

From https://www.cyberciti.biz/faq/ubuntu-change-hostname-command/

```bash
sudo hostname mozzie
```

Update the hostname in the file.
```bash
sudo vi /etc/hostname
```

Update the lines with the old hostname to the new hostname.
```bash
sudo vi /etc/hosts
```

Reboot or change hostname in slurm config.

```
# sudo reboot
# sudo vi /mnt/transient_nfs/slurm/slurm.conf
```


-----

# Rebuilding the VM

2017-09-12: Moved the VM to a different availability zone due to resources. Snapshot 
the VM from the nectar dashboard. Get the AMI number of the snapshot using boto to 
connect to the EC2 API. Use the GVL launcher's custom image option to launch the image. 
Also change the location to uom in persistent_data.yaml (download, edit, re-upload) in
the cm container.

Cloudman doesn't automatically start up if there's no virtual environment. 

As ubuntu, install virtualenv-burrito.

```
curl -sL https://raw.githubusercontent.com/brainsik/virtualenv-burrito/master/virtualenv-burrito.sh | $SHELL
source ~/.venvburrito/startup.sh
mkvirtualenv CM
cd /mnt/cm/
pip install -r requirements.txt
sudo reboot
```

Re-add security groups. Re-activate slurm accounting. Start up stacks web server.

Start up jupyterhub if it fails

```
sudo systemctl start jupyterhub.service
```

Note that launching new workers will use the image. If there's no virtualenv-burrito, 
cloudman won't start and the workers won't connect. 

-----

