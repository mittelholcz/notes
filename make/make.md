# GNU Make


## 1. Bevezetés

A GNU Make egy ún. [_build automation tool_](https://en.wikipedia.org/wiki/Build_automation),
amit eredetileg C nyelvű programok és programkönyvtárak fordítására,
linkelésére, stb. hoztak létre.
Ma már beépített támogatást tartalmaz C++, Fortran, Pascal, TeX stb
projektekhez is.

A `make` parancsot kiadva a GNU Make az aktuális könyvtárban
egy Makefile nevű, kiterjesztés nélküli szöveges fájlt fog keresni.
A Makefile kell tartalmazza a buildelés különböző lépéseinek leírását.
Ezeket a lépéseket ún. _szabályok_ írják le.
A szabályok általános felépítése a következő:

```sh
<target> ... : [<prerequisite> ...]
	[<recipe>]
	...
```

* A _target_ általában a szabály által létrehozandó fájl neve.
  Egy szabály legalább egy target-et kell tartalmazzon, de többet is
  tartalmazhat.
  Ha több target van, akkor egyenként, minden target-re le fognak futni a
  receptek.
  Ezt a lehetőséget értelmesen használni az automatikus változók segítségével
  lehet (l. később).
  A target lehet csak egy végrehajtandó akciónak a neve is, anélkül, hogy
  létrejönne egy ilyen nevű fájl.
  Ezt egy `.PHONY: <target>` sor kell jelölje a Makefile-ban.
  A _phony target_-eken kívül vannak másfajta
  [speciális target-ek](https://www.gnu.org/software/make/manual/html_node/Special-Targets.html#Special-Targets)
  is, de messze ez a legtöbbet használt.
* A  _prerequisite_ egy olyan fájl, amire szükség van a szabály
  végrehajtásához, az új fájl létrehozásához.
  Opcionálisak, egy szabály többet is tartalmazhat, de egyet sem kell
  tartlmazzon.
* A _recipe_ a szabály lefutásakor végrehajtandó (shell)parancs.
  Szintén opcionális, több is lehet egy szabályban, de egy sem kötelező.
  A recepteket tartalmazó soroknak TAB-bal __kell__ kezdődniök!

Példa:

```sh
# Makefile1

all: clean main
.PHONY: all

main: main.o a.o b.o
	g++ -o main main.o a.o b.o

main.o: main.cpp a.h b.h
	g++ main.cpp -c -o main.o

a.o: a.cpp a.h
	g++ a.cpp -c -o a.o

b.o: b.cpp b.h
	g++ b.cpp -c -o b.o

clean:
	rm -f *.o
	rm -f main
.PHONY: clean
```


### 1.1. Futtatás

Egy szabályt futatni a `make <target>` paranccsal lehet,
pl. `make clean`. A shell-ben megszokott TAB-os kiegészítés a target-eknél
is működik.
A Make az első szabályt tekinti defaultnak, ha nem adunk meg egyet sem,
akkor mindig ez fog lefutni.
A fenti példában a `make` hívás megegyezik a `make all` hívással.

A `make` parancsot alapból csak a Makefile-t tartalmazó könyvtárban
lehet kiadni. Ha máshonnan szeretnénk meghívni, akkor a `-C <dir>`
és a `-f <path/to/Makefile>` opciók segítenek. A `-C`-vel lehet könyvtárat
váltani, a `-f`-fel pedig tetszőleges Makefile-t megadni.


### 1.2. Make vs. Bash

Miért jobb Make-et használni, mint egy, a szabályokat tartalmazó
Bash-szkripteket írni?

1. Függőség kezelés. Ha egy szabály futtatásához kell egy másik szabály által
   előállított fájl, akkor a Make azt is futtatni fogja.
   Hogy milyen fájlt hoz létre egy szabály,
   azt a Make csak a _target_-ből tudja.
   A helyes függőségkezeléshez ezért ugyanazt a nevet kell adni _target_-nek,
   mint a létrehozott fájl neve.
2. A Make csak a szükséges szabályokat futtatja.
   Ha egy szabály követelményei régebben lettek módosítva,
   mint az általa létrehozott állomány, akkor azt nem kell futtatni,
   ezzel jelentősen gyorsabb tud lenni, mint egy Bash-szkript (ha csak
   le nem programozzuk ezt is...).

> Most már érthető lehet, miért kell külön jelölni a _phony target_-eket.
Ha egy _phony target_ nincs megjelölve, de van vele egyező nevű
fájl a könyvtárban, akkor a Make nem fogja végrehajtani az adott
szabályt, ha a követelményei régebbiek, mint a vele egyező nevű fájl.
Ezzel szemben a jelölt _phony target_-et tartalmazó szabályok  mindig lefuttak
(már ha szerepelnek a függőségi láncban).


### 1.3. Megjegyzések

* Kommentelni a `#` karakterrel lehet (sor végéig tartó komment).
* A soron levő parancsot a Make kiírja a terminálra. Ez néha zavaró lehet,
  közvetlenül a parancs elé írt `@` prefixszel lehet ezt kikapcsolni. Példa:
  ```sh
  info:
  	echo 'valami'
  	@echo 'valami más'
  .PHONY: info
  ```
* Ha valamelyik parancs hibával tér vissza, akkor a Make is leáll.
  Ezt a `-` prefix-szel kapcsolhatjuk ki. Példa:
  ```sh
  test:
  	@echo 'recipe with dash'
  	-! [ -d / ]
  	@echo 'recipe without dash'
  	! [ -d / ]
  	@echo 'end'
  ```
* A Make alapból a `/bin/sh`-t használja shell-ként (linuxon).
  Ezen a `SHELL` változóval lehet változtatni, pl.
  a `SHELL := /bin/bash` hatására a Make az összes szabályban a Bash-nak adja
  át a recepteket.
  A `<target>: SHELL := /bin/bash` formájú értékadással ezt lehet szűkíteni
  konkrét szabályra.
* A Make minden _recipe_ sort új shell-ben futtat.
  Erre érdemes figyelni, pl.
  ```sh
  new_shell:
  	cd /
  	ls -la
  ```
  nem ugyan az, mint
  ```sh
  new_shell:
  	cd / ; ls -la
  ```
* A fenti tulajdonság miatt gyakori, hogy egy-egy parancssor nagyon hosszú
  lenne, ami az olvashatóság kárára mehet. Ez elkerülhető a sorvégi `\`
  haszálatával, ami a bash-nak a következő sort is az előzővel együtt adja át.
  Példa:
  ```sh
  one_line:
  	cd / ; \
  	ls -la
  ```
* A Makefile tartalmazhat `include <path/to/file> ...` direktívákat is.
  A Make beolvassa az _include_-olt fájlok tartalmát, mielőtt tovább menne.
  Az ilyen fájlok konvenció szerint _.mk_ kiterjesztésűek és tartalmazhatnak
  mindent, amit a Makefile-ok szoktak: változókat, szabályokat vagy
  direktívákat.


## 2. Változók

Változónevek nem tartalmazhatnak `:`, `#` és `=` karaktereket,
továbbá semilyen szóköz jellegű karaktert.
A gyakorlatban csak ASCII nagybetűket, számokat és alulvonást szokás használni.

Változóra hivatkozni általánosan a `$(<variable>)` formában lehet.
A zárójel elhagyható, ha a változónév csak egy karakter.
Ezek alapján

* `echo $(foo)`: jó, kiírja a _foo_ változó értékét;
* `echo $f`: jó, kiírja _f_ változó értékét;
* `echo $foo`: meglepetés, kiírja _f_ változó értékét és utána _oo_-t.

Az összes változó (beépített + saját) értékét a `make -p` parancs listázza ki.


### 2.1. Automatikus változók

A Make rendelkezik pár automatikus változóval. Ezek automatikusan kapnak
értéket, nem kell őket külön definiálni, elég használni.

Gyakrabban használt automatikus változó:

* `$@`: A _target_.
* `$<`: Az első függőség neve.
* `$^`: A függőségek listája, szóközzel elválasztva.

Automatikus változók teljes listája
[itt](https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html).


### 2.2. Implicit szabályok változói

Hogy mik az implicit szabályok, az most nem érdekes, a lényeg, hogy vannak
hozzájuk tartozó változók.
Az automatikus változókhoz hasonlóan nem kell őket definiálni
(bár gyakran szokás kibővíteni vagy felülírni őket).
Teljes lista [itt](https://www.gnu.org/software/make/manual/html_node/Implicit-Variables.html).
Ezek a változók tényleg a _build_-elést célozzák, pl. a _CXX_ az adott
rendszeren alapértelmezett C++ fordítót nevét tartalmazza.
Ez elvileg a platformfüggetlenséget célozza, de azért a Makefile-ok nem erről
híresek.
Amíg nem írunk C/C++ kódot, addig ezekkel nem nagyon lesz dolgunk.


### 2.3. Saját változók

Saját változót kétféleképpen definiálhatunk.

1. `<name> = <value>`: Így lehet az ún. _recursively expanded_ változókat
   definiálni, amikben __nem__ lehet a változót önmagát felhasználni
   a definícióban, mert az rekurzióhoz vezet. Pl.
   a `foo = $(foo) bar` nem a _foo_ változót bővíti _bar_-ral, hanem
   végtelen rekurziós hibálval elszáll.
   A _recursively expanded_ változók definiálásánál használt függvények minden
   hivatkozásnál kiértékelődnek (ez lassítja a Make-et).
   Ha erre a tulajdonságra nincs szükségünk,
   akkor kerüljük a használatát.
2. `<name> := <value>`: Esetleg az ezzel ekvivalens `::=` formában is
   találkozhatunk vele.
   Így lehet _simply expanded_ változókat definiálni. Ezeket lehet rekurzív
   szerkezetű definícióban is haszálni, és a definícióban használt függvények
   és változók csak egyszer értékelődnek ki. Emiatt szabály-specifikus
   értékek tárolására nem jók.

Példa:

```sh
# Makefile2

OBJECTS := main.o a.o b.o
CMD = g++ $< -c -o $@

all: clean main
.PHONY: all

main: $(OBJECTS)
	g++ -o $@ $^

main.o: main.cpp a.h b.h
	$(CMD)

a.o: a.cpp a.h
	$(CMD)

b.o: b.cpp b.h
	$(CMD)

clean:
	$(RM) *.o
	$(RM) main
.PHONY: clean
```


### 2.4. Bash-változók Makefile-ban

Ha egy shell változóra kell hivatkozni akkor azt a `${<variable>}` formában
tehetjük meg. Az egyszerű `$<variable>` alak nem fog jól működni. Például az
`echo $PATH` keresni fogja a `$P` _Make_ változót (és nem _Shell_-változót!),
ami ha nincs definiálva, akkor üres string lesz,
majd kiírja az _ATH_ stringet a terminálra.

Ha arra szorulnánk, hogy egy receptben ne csak használjunk egy létező
változót, hanem ott helybe adjunk neki értéket előbb,
akkor később (de még ugyan abban a receptben) a `$${<variable>}` formában
tudunk rá hivatkozni.
Példa:
```sh
for:
	@PREFIX='make_' ; \
    for file in `ls -1`; \
    do echo $${PREFIX}$${file} ; \
    done
.PHONY: for
```


### 2.5. Megjegyzések

* A változók -- shell szkriptekhez hasonlóan -- mindig string típusúak.
* Egy változót a Make hívásakor is meg lehet adni
  `make <target> <variable>=<value>` formában.
  A Makefile-on belüli értékadással szemben itt nem lehet szóköz az `=` körül.
  A Makefile-ban nem kell feltétlenül definiálva lennie az így megadott
  változónak, de ha előzetesen definiálva volt, akkor felülíródik a híváskor
  megadott értékkel.
* Definiálatlan változó (nem volt definiálva és a Make hívásakor
  sem adtuk meg az értékét) üres stringnek számít, nem vált ki hibát.
* Lehetőség van feltételes értékadásra `<variable> ?= <value>` formában.
  Ez akkor ad értéket a változónak, ha eddig nem volt neki.
* Lehetőség van egy változó bővítésére a `<variable> += <value>` formában.
* Több soros változót a `define <variable> =\n ... endef` direktíva
  segítségével lehet megadni.
  Az `=` után új sort kell kezdeni!
  Példa:
  ```sh
  define MULTILINE =
  @echo 1
  @echo 2
  @echo 3
  endef

  test:
  	$(MULTILINE)
  .PHONY: multi
  ```
* Változó törölhető az `undefine <variable>` direktívával.
* A fentiek segítségével lehetőség van értelmesebb _többszörös target_-ek
  írására.
  Ha egy szabályban több _target_ van, akkor a Make a recepteket a _target_
  minden elemére lefuttatja.
  Példa:
  ```sh
  FRUITS := apple orange

  $(FRUITS):
  	@echo $@
  ```
* Lehetőség van ún. helyettesítő hivatkozás (_substitution reference_)
  használatára is.
  A helyettesítő hivatkozás egy változó értékét szavak listájaként kezeli,
  és minden szóban helyettesítést hajt végre egy megadott minta szerint.
  Alap változatban (`$(<variable>:from=to)`) csak a szavak végére próbálja
  illeszteni a mintát.
  Figyeljünk rá, hogy a változónak csak a nevét kell megadni (`<variable>`),
  de nem kell rá `$(<variable>)` alakban hivatkozni.
  Példa:
  ```sh
  objects := a.o b.o c.o
  sources := $(objects:.o=.cpp) # sources := a.cpp b.cpp c.cpp
  ```
  Általános változatában nem csak a szóvégeket cserélhetjük, hanem bármit,
  a `%`-ot használva joker karakterként.
  A mintában csak egy `%`-ot lehet használni!
  Példa:
  ```sh
  src := from/a.txt from/b.txt from/c.txt
  trg := $(src:from/%=to/%) # src := to/a.txt to/b.txt to/c.txt
  ```
  (Ez igazából a [_patsubst_](https://www.gnu.org/software/make/manual/html_node/Text-Functions.html#Text-Functions)
  string-manipuláló függvény rövidített alakja.)



## 3. Minták

Gyakori eset, hogy sok, nagyon hasonló szabályt kell írni. Pl. minden
_.cpp_ fájlból előállítani az object fájlt (_.o_) ugyanazzal a paranccsal.
Ilyen esetben nem kell minden szabályt külön megírni,
helyette lehet általános mintát készíteni.


### 3.1. Általános minták

A mintakészítés alapja a `%`, ezt fájlnevek (akár _target_,
akár _prerequisite_) bármilyen substringjének helyettesítésére lehet használni.

Az alábbi példában csak a kiterjesztéseket kellett megadni a szabályban,
végrehajtáskor minden, adott kiterjesztésű fájlra lefut a `g++` parancs.

```sh
%.o: %.cpp
	g++ $< -c -o $@
```

A fenti példa egy általános szabályt ad arra, hogyan kell _.cpp_ fájlokból
_.o_ fájlokat létrehozni.
Az így megadott szabályokra a parancssori TAB-os kiegészítés lehetőségét
elveszítjük.
Ha használni szeretnénk ezt a mintát, akkor vagy ki kell írni a létrehozandó
fájl nevét a Make hívásánál (az alábbi példa esetében `make a.o`),
vagy egy olyan _target_-tel kell hívni,
ami függőségként tartalmazza az előállítandó fájlokat,
(mint az alábbi példa esetében `make objects`).

```sh
OBJECTS := a.o b.o

objects: $(OBJECTS)
.PHONY: objects

%.o: %.cpp
	g++ $< -c -o $@
```


### 3.2. Minták láncolása

A mintákat láncolni is lehet.
A Make a köztes lépések fájljait törölni fogja, csak a végeredmény tartja meg.

Az alábbi példában (kifinomult konverziós lépéseken keresztül)
az _a.txt_ és a _b.txt_ fájlokból áll elő az _a.json_ és a _b.json_ fájl,
a köztes _a.tsv_ és _b.tsv_ fájl törlődik.

```sh
RESULTS := a.json b.json
res: $(RESULTS)

%.json: %.tsv
	cp $< $@

%tsv: %.txt
	cp $< $@
```


### 3.3. Statikus minták

Ha nem akarunk általános mintát -- pl mert nem akarunk minden _.cpp_ fájlt
ugyan úgy kezelni, akkor írhatunk _statikus mintákat_ tartalmazó szabályokat
is.

Ezek abban különböznek az általános mintáktól, hogy tartamzniuk kell azon
_target_-ek listáját is, amikre a megadott szabályt alkalmazni kell.
Általános alakja:

```sh
<target> ... : <target-pattern> : <prerequisite-pattern>
	<recipe>
	...
```

A _cél-_ és a _követelmény-minta_ együtt adja meg,
hogyan kell az aktuális célból előállítani a hozzá tartozó követelményt.
Példa:

```sh
# Makefile3

FUNCTIONS := a.cpp b.cpp
OBJECTS := main.o $(FUNCTIONS:.cpp=.o)
CMD = g++ $< -c -o $@

all: clean main
.PHONY: all

main: $(OBJECTS)
	g++ -o $@ $^

main.o: main.cpp $(FUNCTIONS:.cpp=.h)
	$(CMD)

$(FUNCTIONS:.cpp=.o): %.o: %.cpp %.h
	$(CMD)

clean:
	$(RM) *.o
	$(RM) main
.PHONY: clean
```


## 5. Haladóbb lehetőségek

A Make további programnyelvszerű képességekkel is rendelkezik,
de haszálatukhoz kell némi önsanyargató hajlam.

Ezekből kettőt érdemes kiemelni.

1. Lehet [kondícionális](https://www.gnu.org/software/make/manual/html_node/Conditionals.html#Conditionals)
   kifejezéseket alkotni.
2. Lehet beépített [függvényeket](https://www.gnu.org/software/make/manual/html_node/Functions.html#Functions)
   használni.
   Ezekből főleg a [string-manipuláló](https://www.gnu.org/software/make/manual/html_node/Text-Functions.html#Text-Functions)
   függvények lehetnek hasznosak.
   A függvények általános szintaxisa: `$(<function> <parameter> ...)`.
   Példa:
   ```sh
   second: one two
   	@echo $(word 2, $^) # @echo two
   ```



## 6. Irodalom

* [honlap](https://www.gnu.org/software/make/)
* [dokumentáció](https://www.gnu.org/software/make/manual/)
* [wikipédia](https://en.wikipedia.org/wiki/Make_(software))



