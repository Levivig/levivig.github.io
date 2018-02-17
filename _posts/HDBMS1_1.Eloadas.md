---
layout: post
title:  "HDBMS 1 | 1. Előadás"
date:   2018-02-17 22:42:00 +0100
categories: University
---

# HDBMS 1 | 1. Előadás
[A tárgy honlapja](it.inf.unideb.hu/honlap/halado_dbms1)
[Oracle PL/SQL Refrence](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/lnpls/toc.htm)

## PL/SQL
* Procedural Language for SQL
* Kombinálja az SQL adatmanipulációt a procedurális nyelvi feldolgozással
* Ada-szerű szintakszis
* Blokkszerkezetű nyelv
* A köverkező konstrukciókkal egészíti ki az SQL-t:
	* változók és típusok
	* vezérlési szerkezetek
	* alprogramok és csomagok
	* kurzorok és kurzorváltozók
	* kivételkezelés
	* objektumorientált eszközök

## PL/SQL blokk
```sql
[címke]
[Declare
	deklarációs utasítás(ok)]
BEGIN
	végrehajtható utasítás(ok)
	[EXCEPTION
		kivételkezelő]
END [név];
```

* A blokk a PL/SQL alapvető programegysége. Kezelhető önállóan és beágyazható más programegységbe. A blokk megjelenhet bárhol a programban, ahol végrehajtható utasítás állhat.
* A blokknak három alapvető része van, melyek közül az első és utolsó opcionális:
	* *deklarációs*
*	* végrehajtható*
*	* kivételkezelő*
* Egy lokális név a deklarációjától kezdve látszik. A deklarációk hatásköre a blokk végéig tart.
* Címke: A PL/SQL-programban bármely *végrehajtható* utasítás címkézhető. A címke egy azonosító, amelyet a << és >> szimbólumok határolnak, és az utasítás előtt áll.

```sql
BEGIN
	null;
END;
```

```sql
<<pelda>>
BEGIN
	delete from orszagok;
END;
```

## Deklarációs rész
* tipus definíció
* változó deklaráció
* nevesített konstans deklaráció
* kivétel deklaráció
* kurzor definíció
* alprogram definíció
* (pragma)

### Deklaráció
* A deklaráció tárhelyet foglal le a megadott adattípusú értékhez és elnevezi a tárhelyet, hogy hivatkozni tudjuk.
* Az objektumokat deklarálni kell, mielőtt hivatkozzuk őket.
* A deklaráció szerepelhet blokkok, alprogramok, és csomagok deklarációs részében.

### Nevesített konstans
```sql
név CONSTANT típus [NOT NULL] {:= | DEFAULT} kifejezés;
```

```sql
max_napok_szama CONSTANT NUMBER := 366;
```

* A nevesített konstansnál kötelező a kifejezés megadása.
* A nevesített konstansok és változók kezdőértékadása mindig megtörténik valahányszor aktivizálódik az a blokk vagy alprogram, amelyben deklaráltuk őket.

### Változó
```sql
név típus [[NOT NULL] {:= | DEFAULT} kifejezés];
```

```sql
szuletesi_ido DATE;
dolgozok_szama NUMBER NOT NULL DEFAULT 0;
```

* A név az egy azonosító. Az azonosító egy olyan karaktersorozat amely betűvel kezdődik és esetlegesen betűvel, számjeggyel, illetve a $, _, # karakterekkel folytatódhat. Egy azonosító maximális hossza 30 karakter és minden karakter szignifikáns. A PL/SQL a kis- és nagybetűket nem tekinti különbözőnek!
* Ha egy változónak nem adunk explicit kezdőértéket, akkor alapértelmezett kezdőértéke NULL lesz.


* A skaláris változókra és konstansokra (vagy az összetett változók és konstansok skaláris komponenseire) alkalmazhatunk NOT NULL megszorítást.
* A NOT NULL megszorítás miatt az adott elemhez nem rendelhetünk NULL értéket.
* A NOT NULL megszorítással rendelkező elemhez kezdőértéket kell rendelni.

## DBMS_OUTPUT csomag
* A PL/SQL nem tartalmaz I/O utasításokat.
* A DBMS_OUTPUT csomag segítségével üzenetet helyezhetünk el egy belső pufferbe. A PUT_LINE eljárással helyezhetünk el üzenetet a pufferben.
* A puffer tartalmát megjeleníthetjük a képernyőn a következő SQL*Plus utasítással:
SET SERVEROUTPUT ON

```sql
BEGIN
	DBMS_OUTPUT.PUT_LINE('Hello World!');
END
```

### Utasítások
* Üres `NULL;`
	* Átadja a vezérlést a következp utasításnak
	* Hol hasznos?
		* Fejlesztés
		* Kivételkezelés
* Értékadó `x := 10;`
	* Az értékadó utasítás egy változó, mező, kollekcióelem vagy objektumattribútum értékét állítja be. Az értéket mindig egy kifejezés segítségével adjuk meg.
* Ugró `GOTO cimke;`
	* nem használjuk
* Elágaztató
	* Feltételes
		* Feltétel eredménye lehet TRUE, FALSE, NULL
		* A IF-THEN alak esetén a tevékenységet a THEN és az END IF alapszavak közé zárt utasítássorozat írja le. Ezek akkor hajtódnak végre, ha a feltétel értéke igaz. Hamis és NULL feltételértékek mellett az IF utasítás nem csinál semmit.
		* AzIF-THEN-ELSEalakeseténazegyik tevékenységet a THEN és ELSE közötti, a másikat az ELSE és END IF közötti utasítássorozat adja. Ha a feltétel igaz, akkor a THEN utáni, ha hamis vagy NULL, akkor az ELSE utáni utasítássorozat hajtódik végre.
		* A IF_THEN_ENDIFalakegyfeltételsorozatot tartalmaz. Ez a feltételsorozat a felírás sorrendjében értékelődik ki. Ha valamelyik igaz értékű, akkor az utána következő THEN-t követő utasítássorozat hajtódik végre. Ha minden feltétel hamis vagy NULL értékű, akkor az ELSE alapszót követő utasítássorozatra kerül a vezérlés, ha nincs ELSE rész, akkor ez egy üres utasítás
		* A THEN és ELSE után álló utasítások között lehet újabb IF utasítás, az egymásba skatulyázás mélysége tetszőleges.
```sql
IF feltétel
	THEN utasítás [utasítás]
	[ELSIF feltétel
		THEN utasítás [utasítás]...]...
	[ELSE utasítás [utasítás]...]...
END IF;
```

```sql
DECLARE
	v_Nagyobb NUMBER;
	x		  NUMBER := 7;
	y		  NUMBER := 6;
BEGIN
	v_Nagyobb := x;
	IF x < y
		THEN v_Nagyobb := y;
	END IF;
END;
```

```sql
DECLARE
	v_Nagyobb NUMBER;
	x		  NUMBER := 5;
	y		  NUMBER := 6;
BEGIN
	IF x < y
		THEN v_Nagyobb := y;
		ELSE v_Nagyobb := x;
	END IF;
END;
```

```sql
DECLARE
	v_Nagyobb NUMBER;
	x		  NUMBER := 5;
	y		  NUMBER := 7;
BEGIN
	IF x < y THEN
		DBMS_OUTPUT.PUT_LINE('x kisebb, mint y');
		v_Nagyobb := y;
	ELSIF x > y THEN
		DBMS_OUTPUT.PUT_LINE('x nagyobb, mint y');
		v_Nagyobb := x;
	ELSE
		DBMS_OUTPUT.PUT_LINE('x és y egyenlők');
		v_Nagyobb := x;
	END IF;
END;
```

```sql
DECLARE
  v_Nagyobb NUMBER;
	x 		  NUMBER := 5;
	y 		  NUMBER := 7;
	z 		  NUMBER := 6;
BEGIN
	IF x > y
		THEN IF x > z
			THEN v_Nagyobb := x;
			ELSE v_Nagyobb := z; END IF;
		ELSE IF y > z
			THEN v_Nagyobb := y;
			ELSE v_Nagyobb := z; END IF;
	END IF;
END;
```
	* Case utasítás
		* A CASE egy olyan elágaztató utasítás, ahol az egymást kölcsönösen kizáró tevékenységek közül egy kifejezés értékei, vagy feltételek teljesülése szerint lehet választani.
		* Tehát egy CASE utasítás tetszőleges számú WHEN ágból és egy opcionális ELSE ágból áll.
		* Ha a szelektor_kifejezés szerepel, akkor a WHEN ágakban kifejezés áll, ha nem szerepel, akkor feltétel.

```sql
CASE [szelektor_kifejezés]
	WHEN {kifejezés | feltétel}
		THEN utasítás [utasítás]...
	[WHEN {kifejezés | feltétel}
		THEN utasítás [utasítás]...]...
	[ELSE utasítás [utasítás]...]
END CASE;
```
		* Működése:
			* Ha szerepel szelektor_kifejezés
				1. akkor ez kiértékelődik
				2. majd az értéke a felírás sorrendjében hasonlításra kerül a WHEN ágak kifejezéseinek értékeivel.
				3. Ha megegyezik valamelyikkel, akkor az adott ágban a THEN után megadott utasítássorozat hajtódik végre.
				4. Ha a szelektor_kifejezés értéke nem egyezik meg egyetlen kifejezés értékével sem és van ELSE ág, akkor végrehajtódnak az abban megadott utasítások. Ha viszont nincs ELSE ág, akkor a CASE_NOT_FOUND kivétel váltódik ki.
			* Ha a CASE alapszó után nincs megadva szelektor_kifejezés
				* akkor a felírás sorrendjében sorra kiértékelődnek a feltételek és amelyik igaz értéket vesz fel, annak a WHEN ága kerül kiválasztásra.
				* A szemantika a továbbiakban azonos a fent leírtakkal.
```sql
BEGIN
	CASE 2
		WHEN 1 THEN
			DBMS_OUTPUT.PUT_LINE('2 = 1');
		WHEN 1+2 THEN
			DBMS_OUTPUT.PUT_LINE('2 = 1 + 2 ');
		ELSE
			DBMS_OUTPUT.PUT_LINE('egyik sem');
	END CASE;
END
```

```sql
DECLARE
	v_szam	NUMBER;
BEGIN
	v_szam := 10;
	CASE
		WHEN v_Szam MOD 2 = 0
			THEN DBMS_OUTPUT.PUT_LINE('Páros.');
		WHEN v_Szam < 5
			THEN DBMS_OUTPUT.PUT_LINE('Kisebb 5-nél.');
		WHEN v_Szam > 5
			THEN DBMS_OUTPUT.PUT_LINE('Nagyobb 5-nél.');
		ELSE
			DBMS_OUTPUT.PUT_LINE('Ez csak az 5 lehet.');
	END CASE;
END;
```

## Ciklusok
	* **Alapciklus (végtelen ciklus)**
		* Az alapciklusnál nem adunk információt az ismétlődésre vonatkozóan, tehát ha a magban nem fejeztetjük be a másik három lehetőség valamelyikével, akkor végtelenszer ismétel.
```sql
[címke]
LOOP
	utasítás [utasítás]...]...
END LOOP;
```

```sql
DECLARE
	a	NUMBER(5) := 10;
	b	NUMBER(5) := 10;
BEGIN
	LOOP
		b := b / a;
		a := a - 1;
	END LOOP;
END;
```

	* **WHILE ciklus**
		* Ennél a ciklusfajtánál az ismétlődést egy feltétel szabályozza. A ciklus működése azzal kezdődik, hogy kiértékelődik a feltétel. Ha értéke igaz, akkor lefut a mag, majd újra kiértékelődik a feltétel. Ha a feltétel értéke hamis vagy NULL lesz, akkor az ismétlődés befejeződik, és a program folytatódik a ciklust követő utasításon.
		* A WHILE ciklus működésének két szélsőséges esete van. Ha a feltétel a legelső esetben hamis vagy NULL, akkor a ciklusmag egyszer sem fut le (üres ciklus). Ha a feltétel a legelső esetben igaz és a magban nem történik valami olyan, amely ezt az értéket megváltoztatná, akkor az ismétlődés nem fejeződik be (végtelen ciklus).
```sql
[címke]
WHILE feltétel LOOP
	LOOP
END LOOP;
```

```sql

DECLARE
	v_Faktorialis NUMBER(5);
	i             PLS_INTEGER;
BEGIN
	i := 1;
	v_Faktorialis := 1;
	WHILE v_Faktorialis * i < 10**5 LOOP
    v_Faktorialis := v_Faktorialis * i;
    i := i + 1;
  END LOOP;
END;
```
	* **FOR ciklus**
		* A ciklusváltozó egy implicit módon PLS_INTEGER típusúnak deklarált változó, amelynek hatásköre a ciklusmag. (Vagyis a ciklus deklarálja a változót, ezt nekünk nem kell még egyszer megtenni.) Ez a változó rendre felveszi az alsó_határ és felső_határ által meghatározott egész tartomány minden értékét és minden egyes értékére egyszer lefut a mag. Az alsó_határ és felső_határ egész értékű kifejezés lehet. A kifejezések egyszer, a ciklus működésének megkezdése előtt értékelődnek ki.
		* A REVERSE kulcsszó megadása esetén a ciklusváltozó a tartomány értékeit csökkenően, annak hiányában növekvően veszi fel. Megjegyzendő, hogy REVERSE megadása esetén is a tartománynak az alsó határát kell először megadni.
		* A ciklusváltozónak a ciklus magjában nem lehet értéket adni, csak az aktuális értékét lehet felhasználni kifejezésben.
```sql
[címke]
FOR ciklusváltozó
	IN [REVERSE] alsó_határ..felső_határ
	LOOP
		utasítás [utasítás]...
END LOOP [címke];
```

```sql
DECLARE
  v_Osszeg NUMBER(10):=0;
BEGIN
  FOR i IN 1..100 LOOP
    v_Osszeg := v_Osszeg + i;
  END LOOP;
END;
```

```sql
DECLARE
  v_Osszeg NUMBER(10):=0;
BEGIN
FOR i IN REVERSE 1..100 LOOP
    v_Osszeg := v_Osszeg + i;
  END LOOP;
END;
```
	* Kurzor FOR ciklus
	* **Kilépni:** `EXIT [címke] [WHEN feltétel];`
		* Az EXIT utasítás bármely ciklus magjában kiadható, de ciklusmagon kívül nem használható.
		* Az EXIT hatására a ciklus működése befejeződik és a program a követő utasításon folytatódik.
		* Az EXIT címke utasítással az egymásba skatulyázott ciklusok sorozatát fejeztetjük be a megadott címkéjű ciklussal bezárólag.
		* A WHEN utasításrész a ciklus feltételes befejeződését eredményezi. Csak a feltétel igaz értéke mellett nem folytatódik tovább az ismétlődés.
```sql
DECLARE
  v_Osszeg PLS_INTEGER;
  i        PLS_INTEGER;
BEGIN
i := 1;
v_Osszeg := 0; LOOP
    v_Osszeg := v_Osszeg + i;
    IF v_Osszeg >= 100 THEN
EXIT; -- elértük a célt END IF;
    i := i+1;
  END LOOP;
END;
```

```sql

DECLARE
  v_Osszeg PLS_INTEGER;
  i        PLS_INTEGER;
BEGIN
i := 1;
v_Osszeg := 0; LOOP
v_Osszeg := v_Osszeg + i;
EXIT WHEN v_Osszeg >= 100; -- elértük a célt
    i := i+1;
  END LOOP;
END;
```

```sql
DECLARE
  v_Osszeg PLS_INTEGER := 0;
BEGIN
  <<kulso>>
  FOR i IN 1..100 LOOP
FOR j IN 1..i LOOP
v_Osszeg := v_Osszeg + i;
EXIT kulso WHEN v_Osszeg > 100;
    END LOOP;
  END LOOP;
END;
```

	* **Folytatni:** `CONTINUE [címke] [WHEN feltétel];`
		* A CONTINUE utasítás bármely ciklus magjában kiadható, de ciklusmagon kívül nem használható.
		* A CONTINUE hatására kimaradnak a ciklusmag hátralévő utasításai, és a ciklus a következő iterációs lépéssel folytatódik.
		* A CONTINUE címke utasítással az egymásba skatulyázott ciklusok közül az adott címkéjű ciklus folytatódik a következő iterációs lépéssel.
		* Ha a WHEN utasításrész meg van adva, akkor a CONTINUE utasítás csak akkor lép a következő iterációs lépésre, ha a feltétel igaz.
		* A CONTINUE utasítás az Oracle 11g-ben újdonság.

```sql
DECLARE
 x NUMBER := 0;
BEGIN
	LOOP
		DBMS_OUTPUT.PUT_LINE ('x='||x); x := x + 1;
		IF x < 3
			THEN CONTINUE;
		END IF;
  		DBMS_OUTPUT.PUT_LINE('After CONTINUE:x='||x);
  		EXIT WHEN x = 5;
  END LOOP;
	DBMS_OUTPUT.PUT_LINE('After loop:x='||x);
END;
```

```sql
DECLARE
 x NUMBER := 0;
BEGIN
	LOOP
		DBMS_OUTPUT.PUT_LINE('x='||x);
		x := x + 1;
		CONTINUE WHEN x < 3;
		DBMS_OUTPUT.PUT_LINE('After CONTINUE:x='||x);
		EXIT WHEN x = 5;
	END LOOP;
	DBMS_OUTPUT.PUT_LINE('After loop:x='||x);
END;
```

	* Aciklusmagismétlődésérevonatkozóinformációkat (amennyiben vannak) a mag előtt, a ciklus fejében kell megadni. Ezek az információk az adott ciklusfajtára nézve egyediek.
	* Egy ciklus a működését mindig csak a mag első utasításának végrehajtásával kezdheti meg. Egy ciklus befejeződhet, ha
		* az ismétlődésre vonatkozó információk ezt kényszerítik ki;
		* GOTO utasítással kilépünk a magból;
		* az EXIT utasítással befejeztetjük a ciklust;
		* Kivétel váltódik ki.
* SQL utasítások
	* DML
		* INSERT, DELETE, UPDATE
			* kiegészülnek egy RETURNING utasításrésszel, segítségével az érintett sorok alapján számított értéket kaphatunk meg
		* MERGE
			* „UPSERT” funkcionalitás
		* SELECT
			* kiegészül egy INTO (ill. BULK COLLECT INTO) utasításrésszel
	* DCL
		* COMMIT, ROLLBACK, SAVEPOINT




#University/4_Félév/HDBMS
