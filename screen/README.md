# SCREEN


## Alapok

* `screen -list`: futo screen-ek kilistazasa
* `screen -r`: ujracsatlakoas (re-attach) futo screen-hez. Ha tobb screen-hez
  is lehet csatlakozni, kiirja a lehetosegeket (pid-et kell megadni, TAB-os
  kiegeszites mukodik).
* `screen`: uj screen inditasa
* Ctrl-A ?: help-ablak
* Ctrl-A C: uj ablak (create)
* Ctrl-A N: kovetkezo (next) ablak
* Ctrl-A D: ablak levalasztasa a screen-rol (de-attach), screen fut tovabb a
  hatterben
* Ctrl-A K: Aktualis ablak bezarasa (kill). Utolso ablak bezarasaval a screen
  is kilep.


## Egyebek

* Ctrl-A H: Log fajl kiirasa abba a konyvtarva, ahonnan a screen-t inditottuk.
  Csak az aktualis ablak tartalmat irja ki.
* Ctrl-A X: Lezarja az aktualis ablakot, jelszot ker a folytatashoz.


## Irodalom

* honlap: https://www.gnu.org/software/screen/
* tutorialok:
    - [rackaid](https://www.rackaid.com/blog/linux-screen-tutorial-and-how-to/)
    - [aperiodic](http://aperiodic.net/screen/quick_reference)
    - [mattcutts](https://www.mattcutts.com/blog/a-quick-tutorial-on-screen/)

