## Co je skript

Skript je textový soubor, ve kterém jsou příkazy pod sebou. Shell je přečte odshora dolů a provede jeden po druhém. Nic víc.

Všechno, co jsi dosud psal do terminálu, můžeš dát do skriptu. A naopak.

Skript se hodí vždycky, když:

- děláš stejnou věc podruhé,
- je příkazů víc než tři,
- to má dělat někdo jiný než ty,
- to má běžet v noci bez tebe.

## První skript

```bash
nano zaloha.sh
```

Do souboru napiš:

```bash
#!/bin/bash
echo "Zálohuju dokumenty"
cp -r ~/Dokumenty ~/zaloha
echo "Hotovo"
```

Ulož a spusť:

```bash
bash zaloha.sh
```

## Shebang

První řádek `#!/bin/bash` se jmenuje **shebang** a není to komentář, i když tak vypadá.

Říká systému, kterým programem má soubor spustit. Bez něj systém neví, jestli je to bash, Python nebo něco jiného.

| Shebang | Spustí se |
| --- | --- |
| `#!/bin/bash` | bash |
| `#!/bin/sh` | základní shell, méně funkcí |
| `#!/usr/bin/env bash` | bash, ať je kdekoli |
| `#!/usr/bin/env python3` | Python 3 |

**Musí být na úplně prvním řádku** a nesmí před ním být mezera ani prázdný řádek.

## Spouštění

Skript jde spustit třemi způsoby a je mezi nimi rozdíl.

```bash
bash zaloha.sh          nemusí být spustitelný, shebang se ignoruje
./zaloha.sh             musí být spustitelný, řídí se shebangem
source zaloha.sh        běží v aktuálním shellu
```

### Právo na spuštění

```bash
ls -l zaloha.sh
-rw-r--r-- 1 student student 95 Nov 10 09:12 zaloha.sh
```

Chybí `x`, takže `./zaloha.sh` skončí hláškou `Permission denied`.

```bash
chmod +x zaloha.sh
ls -l zaloha.sh
-rwxr-xr-x 1 student student 95 Nov 10 09:12 zaloha.sh
```

### Proč tečka lomítko

```bash
zaloha.sh               command not found
./zaloha.sh             funguje
```

Shell hledá programy jen v adresářích z `PATH` a aktuální adresář v `PATH` není. `./` znamená "v tomhle adresáři".

### Co dělá source

```bash
bash skript.sh          spustí se nový shell, po skončení zanikne
source skript.sh        běží v tomhle shellu
```

Když skript nastaví proměnnou, po `bash skript.sh` ji v terminálu nemáš. Po `source` ano. Proto se `.bashrc` načítá přes `source`.

## Komentáře

```bash
#!/bin/bash
# Záloha dokumentů do domovského adresáře
# Autor: Novák, 3.E

cp -r ~/Dokumenty ~/zaloha     # samotné kopírování
```

Všechno od `#` do konce řádku shell ignoruje. Do hlavičky napiš, co skript dělá. Za tři měsíce to budeš potřebovat ty sám.

## Proměnné ve skriptu

```bash
#!/bin/bash
jmeno="Novák"
pocet=5

echo "Ahoj $jmeno"
echo "Souborů: $pocet"
```

Platí stejná pravidla jako v terminálu:

- kolem `=` **nesmí být mezery**,
- při zápisu bez dolaru, při čtení s dolarem,
- hodnoty s mezerou do uvozovek.

```bash
jmeno = "Novák"      chyba, shell hledá program jmeno
jmeno="Novák"        správně
```

### Složené závorky

```bash
soubor="zaloha"
echo "$soubor.txt"       zaloha.txt
echo "$souborXY"         prázdné, hledá proměnnou souborXY
echo "${soubor}XY"       zalohaXY
```

Když za proměnnou hned pokračuje písmeno, musíš ji uzavřít do `${ }`.

### Uvozovky

```bash
cesta="/home/student/moje dokumenty"
cd $cesta        chyba, bere to jako dvě slova
cd "$cesta"      správně
```

**Proměnnou dávej do uvozovek vždycky.** Je to nejčastější chyba v začátečnických skriptech a projeví se až ve chvíli, kdy má někdo v názvu mezeru.

### Výstup příkazu do proměnné

```bash
dnes=$(date +%Y-%m-%d)
pocet=$(ls | wc -l)
kdo=$(whoami)

echo "Dnes je $dnes, v adresáři je $pocet položek, přihlášen $kdo"
```

Tohle je základ každého užitečného skriptu. Název zálohy s datem:

```bash
cp -r ~/Dokumenty ~/zalohy/zaloha-$(date +%Y-%m-%d)
```

### Počítání

```bash
a=10
b=3
echo $((a + b))        13
echo $((a * b))        30
echo $((a / b))        3
echo $((a % b))        1
```

Bash počítá jen s celými čísly. `10 / 3` je 3, zbytek zahodí.

## Vstup od uživatele

```bash
#!/bin/bash
echo -n "Jak se jmenuješ? "
read jmeno
echo "Ahoj $jmeno"
```

```bash
read -p "Jak se jmenuješ? " jmeno
read -p "Heslo: " -s heslo
read -p "Pokračovat? " -n 1 odpoved
```

| Parametr | Co udělá |
| --- | --- |
| `-p` | rovnou vypíše výzvu |
| `-s` | nezobrazuje psané znaky |
| `-n 1` | čeká na jeden znak, nečeká na Enter |
| `-t 10` | počká 10 sekund a jde dál |

## Argumenty

Hodnoty se dají skriptu předat už při spuštění. To je lepší než `read`, protože skript pak může běžet bez člověka.

```bash
#!/bin/bash
echo "První: $1"
echo "Druhý: $2"
echo "Celkem argumentů: $#"
echo "Všechny: $@"
echo "Jméno skriptu: $0"
```

```bash
./pokus.sh ahoj svete
První: ahoj
Druhý: svete
Celkem argumentů: 2
Všechny: ahoj svete
Jméno skriptu: ./pokus.sh
```

| Proměnná | Obsahuje |
| --- | --- |
| `$0` | jméno skriptu |
| `$1` až `$9` | jednotlivé argumenty |
| `$#` | kolik jich přišlo |
| `$@` | všechny |
| `$?` | návratový kód posledního příkazu |
| `$$` | číslo procesu skriptu |

Praktické použití:

```bash
#!/bin/bash
# Smaže soubory s danou příponou v daném adresáři
# Použití: ./uklid.sh ADRESAR PRIPONA

adresar="$1"
pripona="$2"

echo "Mažu *.$pripona v $adresar"
find "$adresar" -name "*.$pripona" -delete
```

## Návratový kód

Každý skript i příkaz po sobě nechá číslo. Nula znamená v pořádku, cokoli jiného chybu.

```bash
ls /etc
echo $?          0

ls /neexistuje
echo $?          2
```

Vlastní kód nastavíš příkazem `exit`:

```bash
#!/bin/bash
if [ -z "$1" ]; then
    echo "Chybí argument"
    exit 1
fi

echo "Pracuju s $1"
exit 0
```

`exit` skript okamžitě ukončí. To, co je za ním, se neprovede.

Návratový kód využiješ při spojování:

```bash
./zaloha.sh && echo "Záloha OK"
./zaloha.sh || echo "Záloha selhala"
```

## Kam s výpisy

```bash
echo "Běžná informace"
echo "Chyba: soubor nenalezen" >&2
```

Hlášky o chybách posílej na stderr, tedy `>&2`. Pak se dají oddělit od výsledků a nesmíchají se do souboru s daty.

```bash
./skript.sh > vysledek.txt 2> chyby.txt
```

## Hlavička, kterou stojí za to psát

```bash
#!/bin/bash
# nazev.sh - co to dělá
# Použití: ./nazev.sh ARGUMENT
# Autor: Novák, 3.E, listopad 2026

set -u          # spadne při použití nenastavené proměnné
```

`set -u` tě upozorní na překlep v názvu proměnné. Bez něj se překlep tiše změní v prázdný řetězec a skript udělá něco úplně jiného, než jsi chtěl.

## Hledání chyb

```bash
bash -x skript.sh
```

Vypíše každý řádek těsně předtím, než ho provede, a to už s dosazenými proměnnými. Je to nejrychlejší způsob, jak najít, kde se to láme.

```bash
bash -n skript.sh
```

Zkontroluje syntaxi a nic nespustí. Hodí se, než pustíš něco, co maže.

## Celý příklad

```bash
#!/bin/bash
# zaloha.sh - zkopíruje zadaný adresář a uloží ho s datem
# Použití: ./zaloha.sh ADRESAR

set -u

zdroj="$1"
datum=$(date +%Y-%m-%d)
cil="$HOME/zalohy/zaloha-$datum"

mkdir -p "$HOME/zalohy"

cp -r "$zdroj" "$cil"

echo "Uloženo do $cil"
echo "Velikost: $(du -sh "$cil" | cut -f1)"
```

```bash
chmod +x zaloha.sh
./zaloha.sh ~/Dokumenty
```

## Kde se to láme

| Chyba | Proč |
| --- | --- |
| `jmeno = "text"` | kolem `=` nesmí být mezery |
| `Permission denied` | chybí `chmod +x` |
| `command not found` u vlastního skriptu | chybí `./` |
| `$cesta` s mezerou se rozpadne | chybí uvozovky kolem proměnné |
| `bad interpreter: No such file` | soubor má konce řádků z Windows, pomůže `dos2unix` |
| shebang na druhém řádku | musí být úplně první |
| proměnná nastavená ve skriptu po skončení chybí | skript běží ve vlastním shellu, použij `source` |
