# Mezőinformációk

## Főbb elemek:

### Beállítások

| Elem | Érték típusa | Leírás |
|------|--------|--------------|
| `Ütemterv kezdeti dátuma` | dátum | A projekt kezdeti dátuma |
| `Eltolás(nap)` | szám | Eltolási napok száma, csúszka csatolási cellája |
| `Léptetési egység(1-100 nap)` | szám | Léptetési szorzó az eltoláshoz |
| `Léptetési csúszka` | szám | Léptetési cellához csúszka |
| `Aktuális dátum` | dátum | A naptár aktuális kezdő dátuma a beállítások alapján |

### Táblázat

| Elem | Érték típusa | Leírás |
|------|--------|--------------|
| `Megnevezés` | szöveg | A folyamat vagy feladat megnevezése |
| `Kategória` | szöveg | ??? |
| `Felelős` | szöveg | A folyamatért felelős személy neve |
| `Állapot` | szám | A folyamat haladása százalékban meghatározva |
| `Kezdés` | dátum | Kezdés dátuma |
| `Befejezés` | dátum | Befejezés dátuma |
| `Napok száma` | szám | A folyamat telje hossza napokban |

### Naptár

| Elem | Érték típusa | Leírás |
|------|--------|--------------|
| `Hónapsáv` | szöveg | Megjeleníti az aktuális hónapot |
| `Napsáv` | szöveg | Megjeleníti az aktuális napot |
| `Csúszka` | szám | Léptető csúszka |
| `Nap névsáv` | szöveg | Megjeleníti az aktuális napokat név szerint |
| `Naptár` | nincs | Megjeleníti a munkálatokat az adott napot ha van |

# Működési mechanikák, képletek

## Százalékszámítás:

### Főfolyamat százalékszámítás:

A főfolyamatok az alfolyamatok százalék-átlagát mutatják

### Alfolyamat százalékszámítás

* Százalékszámítás képlete:

  ```less
  MIN(1; MAX(HAHIBA((MA() - [@Kezdés]) / ([@Befejezés] - [@Kezdés]); 0); 0))
  ```

* Magyarázat:

  * Az eredmény **0–100% közé van korlátozva** a `MIN` és `MAX` függvényekkel
  * A `HAHIBA` kezeli a zéróosztást, hiba esetén **0 értéket ad vissza**

* Alap képlet:

  ```less
  (MA() - [@Kezdés]) / ([@Befejezés] - [@Kezdés])
  ```

* Fogalmak:

  * `MA()` → mai dátum
  * `[@Kezdés]` → kezdési dátum
  * `[@Befejezés]` → befejezési dátums
  * `MA() - [@Kezdés]` → eltelt napok száma
  * `[@Befejezés] - [@Kezdés]` → teljes időszak napjai
  * **Eredmény** → előrehaladás százalékos aránya

## Feltételes formázás(csíkmechanika)

Kitölti egy előre meghatározott színnel a naptár celláit akkor ha, a kezdeti és  
befejezési dátum intervalluma a naptár intervallumán belülre esik.

* Képletek:
    * Azonos tábla esetén:
        ```less
        =ÉS($F9 <= J$5; $G9 >= J$5)
        ```
        * `$F9` → Kezdés dátuma
        * `$G9` → Befejezés dátuma
        * `J$5` → aktuális nap a naptárban
        ---
    * Másik tábla esetén:
        ```less
        =DARABHATÖBB(Alépítmény! $F$79 : $F$87; "<=" & J$5; Alépítmény! $G$79 : $G$87; ">=" & J$5) > 0
        ```
        * `DARABHATÖBB`: Egy adott feltétel által meghatározott intervallum celláinak számát állapítja meg. Az alap képlet összevonását valósítja meg amikor tartományt kell vizsgálni.

            * Tartomány 1: `Alépítmény! $F$79 : $F$87`

                | Rész | Magyarázat |
                |---|---|
                | `Alépítmény!` | a másik tábla |
                | `$F$79` | az intervallum minimuma(a kezdés dátuma) |
                | `$F$87` | az intervallum maximuma(a kezdés dátuma) |

            * Feltétel 1: `"<=" & J$5` 
                
                | Rész | Magyarázat |
                |---|---|
                | `"<="` | kisebb vagy egyenlő |
                | `&` | szöveg összefűzés |
                | `J$5` | aktuális nap a naptárban |
                
            * Tartomány 2: `Alépítmény! $G$79 : $G$87`

                | Rész | Magyarázat |
                |---|---|
                | `Alépítmény!` | a másik tábla |
                | `$G$79` | az intervallum minimuma(a befejezés dátuma) |
                | `$G$87` | az intervallum maximuma(a befejezés dátuma) |

            * Feltétel 2: `">=" & J$5` 
                
                | Rész | Magyarázat |
                |---|---|
                | `">="` | nagyobb vagy egyenlő |
                | `&` | szöveg összefűzés |
                | `J$5` | aktuális nap a naptárban |
        ---
