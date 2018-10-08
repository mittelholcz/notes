# Docker

## Info

* [docker](https://www.docker.com/) (honlap)
* [docs](https://docs.docker.com/)
* install docker-ce: [ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/), [debian](https://docs.docker.com/install/linux/docker-ce/debian/), [centos](https://docs.docker.com/install/linux/docker-ce/centos/)
* [post-installation steps](https://docs.docker.com/install/linux/linux-postinstall/) (fejlesztői környezetnél a felhasználót hozzá kell adni a *docker* csoporthoz)
* [get-started](https://docs.docker.com/get-started/#docker-concepts)
* [docker hub](https://hub.docker.com/)
* [docs: docker hub](https://docs.docker.com/docker-hub/)
* irodalom: ebből sok van de Viktor Farcic DevOps ToolKit sorozata szerintem nagyon jó [link](https://leanpub.com/u/vfarcic)

## Hasznos parancsok

### Infók

* `docker --version`: verzió 
* `docker version`: mindenféle egyéb infó kiírása a telepítésről
* `docker info`: még több egyéb infó kiírása a telepítésről

### Image-ek

* `docker build -t <tagname> <dir>`: új image buildelése a megadott könyvtárban (amiben a Dockerfile található), és feltaggelése (ezen a néven futtatható később)
* `docker image ls`: lokálisan rendelkezésreálló image-ek listázása
* `docker image rm <imageID>`: image törlése

### Konténerek
* `docker run  <image>`: image futtatása (konténer létrehozása, ugyanis a konténer az egy futó image-példány)
    * `-p <host_port>:<guest_port>`: a konténerben (Dockerfile-ban) EXPOSE-ált port mappelése a hostgép egy portjára
    * `-d`: *detached mode*, konténer háttérben futtatása
* `docker container ls`: konténerek listázása
    * `-a`: minden konténer (default: csak a futó konténerek)
* `docker container stop <containerID>`: futó konténer leállítása
* `docker container kill `<containerID>: futó konténer leállítása minden körülmények között
* `docker container rm <containerID>`: konténer törlése


## Dockerfile

valami


## Hasznosabb image-ek

* hivatalos image-ek [listája](https://github.com/docker-library/docs) és [dokumentációja](https://docs.docker.com/docker-hub/official_repos/)
    * [debian](https://hub.docker.com/_/debian/)
    * [python](https://hub.docker.com/r/_/python/)
    * [nginx](https://hub.docker.com/r/library/nginx/)
    * [mariadb](https://hub.docker.com/_/mariadb/)
    * [postgresql](https://hub.docker.com/_/postgres/)
    * [java](https://hub.docker.com/_/openjdk/) (openjdk)
* nem hivatalosak
    * [ubuntu16.04 + django + uwsgi + nginx](https://hub.docker.com/r/dockerfiles/django-uwsgi-nginx/) ([repo](https://github.com/dockerfiles/django-uwsgi-nginx))
    * [django + nginx](https://hub.docker.com/r/bchabord/django-nginx/)
    * [python3 + django + gunicorn](https://hub.docker.com/r/alang/django/)


