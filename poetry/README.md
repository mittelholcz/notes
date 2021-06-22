# Poetry

A *poetry* egy csomagkezelő szoftver python-hoz, ami segít függőségek
kezelésében, a virtuális környezetek menedzselésében és új csomagok
létrehozásában.

- [honlap](https://python-poetry.org/)
- [github](https://github.com/python-poetry/poetry)
- [dokumentáció](https://python-poetry.org/docs/)

## Telepítés

```txt
curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -
```

## Új projekt létrehozása

Üres vagy nem létező könvtárnál:

```txt
poetry new path/to/library
```

Nem üres könyvtárnál:

```txt
poetry init
```

A `new` egy egész könyvtárstruktúrát hoz létre a projektnek, benne a python
függőségeket leíró *pyproject.toml* fájlal.  Az `init` csak a *pyproject.toml*
fájlt hozza létre (alapból interakívan).

## Csomag hozzáadása

Ha új python csomagot akarunk felvenni, vagy szerkesszük közvetlenül a
*pyproject.toml* fájlt, vagy használjuk a `poetry add package_name` parancsot.
Csomagokat meghatározásának módjait (pl. verzió) l.
[itt](https://python-poetry.org/docs/dependency-specification/).

## Környezet telepítése

Új virtuális könrnyezet létrehozása a *pyproject.toml* alapján:

```txt
poetry install
```

Ha eddig nem létezett, akkor ez egy *poetry.lock* fájlt is létre fog hozni. Ha
már létezett, akkor az alapján fog létrejönni a környezet. A *poetry.lock*
fájlt érdemes verziókövető alatt tartani, így többen tudják pontosan ugyanazt a
fejlesztői környezetet rekonstruálni.

## Környezet frissítése

Frissebb (de a megszorításoknak megfelelő) verziójú csomagok telepítése, ill. a
*pyproject.toml*-ba újonan bekerült, de még nem telepített csomagok telepítése:

```txt
poetry update
```

## Környezet használata

Ha csak egy parancsot szeretnénk kiadni (*Makefile*-ban is használható):

```txt
poetry run command_name
```

Ha új *shell*-t szeretnénk, amiben a környezet akív:

```txt
poetry shell
```

Kilépni az `exit` paranccsal lehet (a `deactivate` csak a környezetből lép ki,
de a *shell* -- az könrnyezeti változókkal együtt -- marad).

## Terjesztés

Telepíthető fájlok létrehozása (a *dist/* könyvtárban):

```txt
poetry build
```

Csomag feltöltése a [PyPI](https://pypi.org/)-ra:

```txt
poetry publish
```

Ehhez persze előbb regisztrálni kell a PyPI-n és a hitelesítést is konfigurálni
kell (l.
[itt](https://python-poetry.org/docs/repositories/#configuring-credentials)).
További hasznos beállítások a *pyproject.toml* fájlban (l.
[itt](https://python-poetry.org/docs/pyproject/)).

## Konfigurálás

Aktuális beállítások listázása:

```txt
poetry config --list
```

Egy beállítás megadása

```txt
poetry config kulcs érték
```

A beállítások alapból globálisak. Lokális konfigurálás, kulcs-érték párok,
részletek a [dokumentációban](https://python-poetry.org/docs/configuration/).

---

### Alternatívák

- [conda](https://docs.conda.io/en/latest/)
- [pdm](https://pdm.fming.dev/)
- [pipenv](https://pipenv.pypa.io/en/latest/)
- [pipx](https://pypa.github.io/pipx/)

