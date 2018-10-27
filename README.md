# Copy APT repos

Docker image to copy APT repos (one-time mirror to local disk).

See also [docker-yum-repo](https://github.com/adoroszlai/docker-yum-repo) for copying YUM repos.

## Usage

Define (customize) the following variables:

```bash
REPO_DIR=~/data/repos
REPO_HOST=repo
```

 * `REPO_DIR`: the directory on the host where repos are mirrored to
 * `REPO_HOST`: the name of the host that serves local repos (add this to `/etc/hosts` with the appropriate IP)

### Copy APT repo

```bash
docker run --rm -v ${REPO_DIR}:/var/spool/apt-mirror/mirror -e REPO_HOST apt-mirror \
  http://public-repo-1.hortonworks.com/HDP/debian7/2.x/updates/2.6.3.0/hdp.list
```

By default `apt-mirror` only copies one architecture (`amd64` for this Docker image).  To mirror more than one, pass the list of architectures, eg.:

```bash
docker run --rm -v ${REPO_DIR}:/var/spool/apt-mirror/mirror -e REPO_HOST apt-mirror \
  http://public-repo-1.hortonworks.com/HDP/debian7/2.x/updates/2.6.3.0/hdp.list \
    amd64 i386
```

A local version of the APT list file is also created with URLs changed to point to `${REPO_HOST}`:

```
deb http://repo/public-repo-1.hortonworks.com/HDP/ubuntu16/3.x/updates/3.0.0.0 HDP main
deb http://repo/public-repo-1.hortonworks.com/HDP-UTILS-1.1.0.22/repos/ubuntu16 HDP-UTILS main
```

### Serve local repos

Local repos can be served by running nginx or some other web server.

```bash
docker run -d --name ${REPO_HOST} -p 80:80 -h ${REPO_HOST} -v ${REPO_DIR}:/usr/share/nginx/html:ro nginx:alpine
```
