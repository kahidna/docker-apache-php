Table of Contents
-------------------

 * [Installation](#installation)
 * [Quick Start](#quick-start)
 * [Persistence](#development-persistence)
 * [Linked to other container](#linked-to-other-container)
 * [Logging](#logging)
 * [Upgrading](#upgrading)
 * [Adding PHP-extension](#adding-php-extension)
 * [Out of the box](#out-of-the-box)

Installation
-------------------

 * [Install Docker](https://docs.docker.com/installation/) or [askubuntu](http://askubuntu.com/a/473720)
 * Pull the latest version of the image.
 
```bash
docker pull romeoz/docker-apache-php
```

or other versions (5.6, 5.5, 5.4 or 5.3):

```bash
docker pull romeoz/docker-apache-php:5.4
```

Alternately you can build the image yourself.

```bash
git clone https://github.com/romeoz/docker-apache-php.git
cd docker-apache-php
docker build -t="$USER/docker-apache-php" .
```

Quick Start
-------------------

Run the application image:

```bash
docker run --name app -d -p 8080:80 romeoz/docker-apache-php
```

The simplest way to login to the app container is to use the `docker exec` command to attach a new process to the running container.

```bash
docker exec -it app bash
```

Development/Persistence
-------------------

For development a volume should be mounted at `/var/www/app/`.

The updated run command looks like this.

```bash
docker run --name app -d -p 8080:80 \
  -v /host/to/path/app:/var/www/app/ \
  romeoz/docker-apache-php
```

This will make the development.

Linked to other container
-------------------

As an example, will link with RDBMS PostgreSQL. 

```bash
docker run --name db -d romeoz/docker-postgresql
```

Run the application image:

```bash
docker run --name app -d -p 8080:80 \
  --link db:db \
  -v /host/to/path/app:/var/www/app/ \
  romeoz/docker-apache-php
```

Adding PHP-extension
-------------------

Install `php5-mongo`:

```bash
sudo docker exec -it app bash -c 'apt-get update && apt-get install php5-mongo && rm -rf /var/lib/apt/lists/*'
```

>See installed php-extension: `sudo docker exec -it app php -m`

Upgrading
-------------------

To upgrade to newer releases, simply follow this 3 step upgrade procedure.

- **Step 1**: Stop the currently running image

```bash
docker stop app
```

- **Step 2**: Update the docker image.

```bash
docker pull romeoz/docker-apache-php
```

- **Step 3**: Start the image

```bash
docker run --name app -d [OPTIONS] romeoz/docker-apache-php
```

Logging
-------------------

All the logs are forwarded to stdout and sterr. You have use the command `docker logs`.

```bash
docker logs app

```

####Split the logs

You can then simply split the stdout & stderr of the container by piping the separate streams and send them to files:

```bash
docker logs app > stdout.log 2>stderr.log
cat stdout.log
cat stderr.log
```

or split stdout and error to host stdout:

```bash
docker logs app > -
docker logs app 2> -
```

####Rotate logs

Create the file /etc/logrotate.d/docker-containers with the following text inside:

```
/var/lib/docker/containers/*/*.log {
    rotate 31
    daily
    nocompress
    missingok
    notifempty
    copytruncate
}
```
> Optionally, you can replace `nocompress` to `compress` and change the number of days.

Out of the box
-------------------
 * Ubuntu 14.04.3/12.04.5 (LTS)
 * Apache 2.4.x/2.2.x
 * PHP 5.3/5.4/5.5/5.6
 * Composer

>Environment depends on the version of PHP.

License
-------------------

Apache + PHP container image is open-sourced software licensed under the [MIT license](http://opensource.org/licenses/MIT)