# Docker on GVL 4.1

-----

# Docker install

On GVL 4.1 (Ubuntu 14.04):
```
sudo apt-get update

# Extra stuff needed for 14.04
sudo apt-get install -y --no-install-recommends \
    linux-image-extra-$(uname -r) \
    linux-image-extra-virtual

sudo apt-get install -y --no-install-recommends \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common

curl -fsSL https://apt.dockerproject.org/gpg | sudo apt-key add -

# Verify key
apt-key fingerprint 58118E89F3A912897C070ADBF76221572C52609D
```

Install docker:
```
sudo add-apt-repository \
       "deb https://apt.dockerproject.org/repo/ \
       ubuntu-$(lsb_release -cs) \
       main"

sudo apt-get update

sudo apt-get -y install docker-engine
```

Change location for the docker data directory.

Create a new directory to store docker data in `/mnt/transient_nfs/`.

```
mkdir /mnt/transient_nfs/docker_data
```

Edit `/etc/default/docker` to include:
```
DOCKER_OPTS="-g /mnt/transient_nfs/docker_data"
```

Restart docker.
```bash
sudo service docker restart
```

Test if docker works.
```
sudo docker run hello-world
```

----------

# Docker containers

## Example: Shiny server

```
sudo docker pull rocker/shiny

# Test if it's working
sudo docker run --rm -p 3838:3838 rocker/shiny
# Then quit
```

Add security group ingress port 3838 then check <IP>:3838

Launch in detached mode with mounted apps.
```
sudo docker run --rm -d \
    -p 3838:3838 \
    -v /path/to/apps/:/srv/shiny-server/ \
    -v /path/to/logs/:/var/log/shiny-server/ \
    rocker/shiny
```

### Configure nginx

In GVL4.1, nginx is already configured for galaxy, jupyterhub, etc. and some
variables are already set.

Edit `sites-enabled/default.locations` or create a new locations file:
```bash
sudo vi /etc/nginx/sites-enabled/shiny.locations
```

Add the following:
```
         location /shiny/ {
            rewrite ^/shiny/(.*)$ /$1 break;
            proxy_pass http://localhost:3838;
            proxy_redirect http://localhost:3838/ $scheme://$host/shiny/;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
            proxy_read_timeout 20d;
        }
```

Restart nginx
```bash
sudo service nginx restart
```

Check `<IP>`/shiny

Install R packages by entering container.

```bash
sudo docker exec -it <container_name> bash
R
# install packages
# exit R
# exit container
```

-----

## RStudio example

```
sudo docker pull rocker/rstudio

# Test if it's working
sudo docker run -p 3838:8787 rocker/rstudio
```

Username and password: `rstudio`


## Jupyter notebook example

```
sudo docker pull jupyter/datascience-notebook

sudo docker run -it --rm -p 8888:8888 jupyter/datascience-notebook
```
