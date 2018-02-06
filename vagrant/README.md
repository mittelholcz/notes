# VAGRANT


## Mire jó?

Virtuális gépet lehet vele könnyen létrehozni, ami elő van konfigurálva,
hogy lehessen fejlesztésnél használni távoli webszerver szimulálására.

A Vagrant nem virtualizációs réteg önmagában, csak egy absztrakciós szint
kölönböző virtualizáló szoftverek felett (VirtualBox, WMware, stb).
Ajánlott virtualizálás: [VirtualBox](https://www.virtualbox.org/)

A gépek menedzselésére használható konfiguráció menedzser (Puppet, Chef, stb),
a gépek hordozhatók, megoszthatók.


## Munkamenet (webfejlesztés)
1. virtuális gép felállítása;
    webszerver (apache, stb) és egyéb dolgok telepítése és konfigurálása
2. megosztott könyvtár használatba vétele:
    - host: létrehozunk egy könyvtárat a vagrant-könyvtáron belül
      (`mkdir <projekt>`) és ebben fejlesztünk
    - guest: a virtuális gépen a megosztott könyvtárat a menfelelő helyre
      link-eljük (pl. `sudo ln -s /vagrant/<projekt> /var/www/`), itt a
      fejlesztésünk rögtön látszani fog
3. a host-on ellenőrizzük az eredményt (böngészőben)


## Telepítés

1. [letöltés](https://www.vagrantup.com/downloads.html) (ált. a debian/64-bit
   lesz a jó)
2. `dpkg install vagrant*.deb`
3. `vagrant plugin install vagrant-cachier` (ez telepíti a
   [*vagrant-cachier*](https://github.com/fgrehm/vagrant-cachier)
   plugint, ami cache-eli a csomagokat, jelentősen gyorsabbá téve az új
   rendszerek felállítását -- a csomag jelenleg karbantartatlan)
4. `vagrant plugin install vagrant-vbguest` (ez telepíti a
   [*vagrant-vbguest*](https://github.com/dotless-de/vagrant-vbguest)
   plugint, ami az adott verzióju VirtualBox-hoz a megfelelő guest-et telepíti,
   de nem vagyok benne biztos, hogy mindig megfelelően működik)

Megj: elérhető plugin-ok
[listája](https://github.com/hashicorp/vagrant/wiki/Available-Vagrant-Plugins)

## Alap parancsok
* könyvtár inicializálása:
    ```bash
    vagrant init [<box>]
    ```
    A Vagrant-os gépnek kell egy külön könyvtár, az egyes `vagrant*`
    parancsokat ezen belül kell kiadni, és csak az ide helyezett vagrant-gépre
    lesz hatásuk.
    A `vagrant init` létrehoz egy alap `Vagrantfile`-t az aktuális könyvtárban.
    Ez a gép alap konfigurációs állománya (beállítási lehetőségeket l.
    [itt](https://www.vagrantup.com/docs/vagrantfile/)).
    Ha meg volt adva 'box' is, akkor az már szerepel a fájlban
    (a `config.vm.box=<box>` sorban).
    Ajánlottak a "hashicorp" és a "bento" namespace-ben lévő box-ok
    (l. [itt](https://app.vagrantup.com/bento)).
* új box hozzáadása:
    ```bash
    vagrant box add <box>
    ```
    Ha az init-nél megadtuk a box-ot is, akkor ez a lépés nem kell. Ha csak
    simán `vagrant init `-eltünk, akkor a `Vagrantfile`-ban is szerkeszteni
    kell a `config.vm.box=<box>` sort. Egy box-ot csak egyszer kell add-olni,
    onnantól bárhány projektben használható.
* vagrant-gép indítása:
    ```bash
    vagrant up
    ```
* bejelentkezés (könyvtárból):
    ```bash
    vagrant ssh
    ```
    Kilépés Ctrl + D.
* Gép felfüggesztése és felébresztése:
    ```bash
    vagrant suspend
    vagrant resume
    ```
* Gép kikapcsolása:
    ```bash
    vagrant halt
    ```
* Gép törlése:
    ```bash
    vagrant destroy
    ```
    Ez csak a helyi gépet törli, nem magát a box-fájlt. Azaz nem kell
    `vagrant box add`-ot használni legközelebb.
* Box frissítése/törlése:
    ```bash
    vagrant box update
    vagrant box remove
    ```


## Megosztott könyvtár

A virtuális gépen a `/vagrant` könyvtár meg van osztva a host géppel:
`guest:/vagrant = host: vagrant box gyökérkönyvtár` (ahol a virtuális géphez
tartozó `Vagrantfile` is található).


## Felkészülés ([provisioning](https://www.vagrantup.com/docs/provisioning/))

A `Vagrantfile` `config.vm.provision` sorában lehet megadni a Vagrant-környezet
létrehozásakor automatikusan lefutó programokat (shell, ansible, stb).

A *provisioning* alapvetően az első `vagrant up` parancsnál fut le, később csak
akkor, ha expliciten meghívják:

* `vagrant up --provision` indításnál
* `vagrant provision` futó környezetnél

Futtatandó parancsok megadása:

1. shell parancsok megadása a Vagrantfile-ban
   ```bash
   config.vm.provision :shell, inline: <<-SHELL
   # ...
   SHELL
   ```
2. Külső szkript megadása a Vagrant-nak (a `path` a Vagrantfile-hoz képest 
   relatív):
   ```bash
   config.vm.provision :shell, path: "bootstrap.sh"
   ```

## Hálózat ([network](https://www.vagrantup.com/docs/networking/))

A `Vagrantfile` `config.vm.network` sorában lehet konfigurálni a hálózati
dolgokat. Érdemes a *port forwarding*-ot beállítani:
```bash
config.vm.network :forwarded_port, guest: 80, host: 8080
```
Ez azt jelenti, hogy a guest 80-as portjának kimenete átirányítódik a host
8080-as portjára, azaz böngészőben megnyitva a http://127.0.0.1:8080 címet,
láthatjuk a fejlesztésünk eredményét.


## Megjegyzesek
* web2py és Django esetén a standard 127.0.0.1-es IP-cím nem lesz jó. A
    guest-en úgy kell indítani a webszervert, hogy az a 0.0.0.0-ás IP-címet
    használja.

    ```bash
    # web2py:
    python2 web2py --nogui -i 0.0.0.0 -p 80
    # django:
    ./manage.py runserver 0.0.0.0:80
    ```
    Ekkor a böngészőt a
    [http://0.0.0.0:8080](http://0.0.0.0:8080) címen kell
    megnyitni (l.
    [stackoverflow](http://stackoverflow.com/questions/18157353/connection-reset-when-port-forwarding-with-vagrant)).


## Infók
* [dokumentáció](https://www.vagrantup.com/docs/)
* [telepítés](https://www.vagrantup.com/docs/installation/)
* [Vagrantfile konfiguráció](https://www.vagrantup.com/docs/vagrantfile/)
* [command line interface](https://www.vagrantup.com/docs/cli/)

## Olvasnivalók (narrcat-hoz, de ide is ide rakom)

* [ubuntu + apache + vagrant](http://www.websightdesigns.com/posts/view/how-to-configure-an-ubuntu-web-server-vm-with-vagrant)
* [web2py doc: deploy](http://web2py.com/books/default/chapter/29/13#Move-the-handler-script)
* [vagrant for local web dev](http://tech.osteel.me/posts/2015/01/25/how-to-use-vagrant-for-local-web-development.html)
* [vagrant doc: private network](https://www.vagrantup.com/docs/networking/private_network.html)
* [vagrant + ansible + web2py + https](https://github.com/LarsLipinski/dev-vagrant-web2py-ubuntu) 
