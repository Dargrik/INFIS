## Proč to potřebuješ

Dosud skript dělal vždycky to samé. Podmínka ho nechá reagovat na situaci, cyklus ho nechá udělat stejnou věc se sto soubory.

Tohle jsou dvě věci, které z posloupnosti příkazů dělají program.

## Podmínka

```bash
if PODMÍNKA; then
    příkazy
fi
```

```bash
#!/bin/bash
if [ -f ~/.bashrc ]; then
    echo "Soubor existuje"
fi
```

Celý zápis:

```bash
if [ "$1" = "start" ]; then
    echo "Spouštím"
elif [ "$1" = "stop" ]; then
    echo "Zastavuju"
else
    echo "Použití: $0 start|stop"
fi
```

`fi` je `if` pozpátku a ukončuje blok. Stejně tak `esac` ukončuje `case`.

**Mezery v hranatých závorkách jsou povinné.** `[$a = "x"]` je chyba, musí být `[ "$a" = "x" ]`. Hranatá závorka je totiž ve skutečnosti příkaz, ne jen znak.

## Testy na soubory

| Test | Pravda, když |
| --- | --- |
| `-e soubor` | existuje, ať je to cokoli |
| `-f soubor` | existuje a je to obyčejný soubor |
| `-d adresar` | existuje a je to adresář |
| `-r soubor` | jde číst |
| `-w soubor` | jde do něj zapisovat |
| `-x soubor` | jde spustit |
| `-s soubor` | existuje a není prázdný |
| `-L soubor` | je symbolický odkaz |

```bash
if [ -d "$HOME/zalohy" ]; then
    echo "Adresář pro zálohy je připraven"
else
    mkdir "$HOME/zalohy"
fi
```

## Porovnávání čísel

| Zápis | Znamená |
| --- | --- |
| `-eq` | rovná se |
| `-ne` | nerovná se |
| `-gt` | větší než |
| `-ge` | větší nebo rovno |
| `-lt` | menší než |
| `-le` | menší nebo rovno |

```bash
pocet=$(ls | wc -l)

if [ "$pocet" -gt 100 ]; then
    echo "V adresáři je přes sto položek"
fi
```

## Porovnávání textu

| Zápis | Znamená |
| --- | --- |
| `=` | shodné |
| `!=` | různé |
| `-z "$a"` | řetězec je prázdný |
| `-n "$a"` | řetězec není prázdný |

```bash
if [ -z "$1" ]; then
    echo "Chybí argument"
    exit 1
fi
```

**Nepleť si `-eq` a `=`.** Čísla se porovnávají přes `-eq`, text přes `=`. Při záměně to buď spadne, nebo tiše vrátí nesmysl.

## Spojování podmínek

```bash
if [ -f "$soubor" ] && [ -r "$soubor" ]; then
    echo "Soubor existuje a jde číst"
fi

if [ "$a" = "ano" ] || [ "$a" = "a" ]; then
    echo "Souhlas"
fi

if [ ! -d "$adresar" ]; then
    echo "Adresář neexistuje"
fi
```

| Zápis | Znamená |
| --- | --- |
| `&&` | a zároveň |
| `\|\|` | nebo |
| `!` | naopak |

## Dvojité závorky

```bash
if [[ "$jmeno" == zaloha* ]]; then
    echo "Jméno začíná na zaloha"
fi

if (( pocet > 100 )); then
    echo "Přes sto"
fi
```

`[[ ]]` je novější verze `[ ]`, umí masky a je bezpečnější při prázdné proměnné. `(( ))` je na čistě číselné porovnání a uvnitř se píše normálně `>` a `<`.

V bashi používej `[[ ]]`. Ve skriptech, které mají běžet i jinde, zůstaň u `[ ]`.

## Podmínka podle návratového kódu

Do `if` se dá dát rovnou příkaz. Bere se jeho návratový kód, nula znamená pravdu.

```bash
if grep -q "student" /etc/passwd; then
    echo "Uživatel student existuje"
fi

if ping -c 1 seznam.cz &> /dev/null; then
    echo "Internet funguje"
else
    echo "Internet nefunguje"
fi
```

`-q` u `grep` znamená, že nic nevypíše a jen nastaví kód. Přesně pro tohle použití.

## case

Když větví je víc, je `case` přehlednější než řada `elif`.

```bash
case "$1" in
    start)
        echo "Spouštím"
        ;;
    stop)
        echo "Zastavuju"
        ;;
    restart)
        echo "Restartuju"
        ;;
    *)
        echo "Použití: $0 start|stop|restart"
        exit 1
        ;;
esac
```

Každá větev končí `;;`. Hvězdička je všechno ostatní a patří na konec. Větve můžou obsahovat masky, třeba `*.txt)` nebo `a|ano)`.

## Cyklus for

```bash
for promenna in SEZNAM; do
    příkazy
done
```

```bash
for i in 1 2 3 4 5; do
    echo "Kolo $i"
done

for jmeno in Novák Svoboda Dvořák; do
    echo "Ahoj $jmeno"
done
```

### Přes soubory

```bash
for soubor in *.txt; do
    echo "Zpracovávám $soubor"
    wc -l "$soubor"
done
```

Tohle je nejčastější použití vůbec. Hromadné přejmenování:

```bash
for soubor in *.txt; do
    mv "$soubor" "${soubor%.txt}.bak"
done
```

`${soubor%.txt}` utne `.txt` z konce.

### Číselný rozsah

```bash
for i in {1..10}; do
    echo "$i"
done

for i in {1..20..2}; do
    echo "$i"
done

for ((i=1; i<=10; i++)); do
    echo "$i"
done
```

### Přes výstup příkazu

```bash
for uzivatel in $(cut -d: -f1 /etc/passwd); do
    echo "Uživatel: $uzivatel"
done
```

## Cyklus while

Opakuje, dokud podmínka platí.

```bash
pocet=1
while [ "$pocet" -le 5 ]; do
    echo "Kolo $pocet"
    pocet=$((pocet + 1))
done
```

**Na změnu proměnné uvnitř nesmíš zapomenout.** Bez `pocet=$((pocet + 1))` poběží cyklus donekonečna a zastavíš ho jen <kbd>Ctrl</kbd>+<kbd>C</kbd>.

### Čtení souboru po řádcích

```bash
while read radek; do
    echo "Řádek: $radek"
done < /etc/passwd
```

```bash
while IFS=: read jmeno heslo uid zbytek; do
    echo "$jmeno má UID $uid"
done < /etc/passwd
```

`IFS=:` říká, že sloupce jsou oddělené dvojtečkou. Takhle se čte CSV.

### until

```bash
until ping -c 1 seznam.cz &> /dev/null; do
    echo "Čekám na síť"
    sleep 5
done
echo "Síť je"
```

`until` je obrácený `while`, běží dokud podmínka **ne**platí.

## break a continue

```bash
for soubor in *.txt; do
    if [ ! -r "$soubor" ]; then
        continue
    fi
    echo "Zpracovávám $soubor"
done
```

| Příkaz | Co udělá |
| --- | --- |
| `break` | cyklus ukončí |
| `continue` | přeskočí na další kolo |

## Nekonečný cyklus s menu

```bash
while true; do
    echo "1) Výpis  2) Datum  3) Konec"
    read -p "Volba: " volba
    case "$volba" in
        1) ls ;;
        2) date ;;
        3) break ;;
        *) echo "Neznámá volba" ;;
    esac
done
```

## Celý příklad

```bash
#!/bin/bash
# kontrola.sh - projde adresář a vypíše přehled
# Použití: ./kontrola.sh ADRESAR

set -u

if [ $# -ne 1 ]; then
    echo "Použití: $0 ADRESAR" >&2
    exit 1
fi

adresar="$1"

if [ ! -d "$adresar" ]; then
    echo "Adresář $adresar neexistuje" >&2
    exit 2
fi

velke=0
prazdne=0

for soubor in "$adresar"/*; do
    [ -f "$soubor" ] || continue

    if [ ! -s "$soubor" ]; then
        echo "Prázdný: $soubor"
        prazdne=$((prazdne + 1))
    elif [ $(stat -c%s "$soubor") -gt 1000000 ]; then
        echo "Velký:   $soubor"
        velke=$((velke + 1))
    fi
done

echo
echo "Prázdných souborů: $prazdne"
echo "Větších než 1 MB:  $velke"
```

```bash
chmod +x kontrola.sh
./kontrola.sh ~/Stažené
```

## Kde se to láme

| Chyba | Proč |
| --- | --- |
| `[$a = "x"]` | kolem hranatých závorek musí být mezery |
| `if [ $a -eq 5 ]` a `$a` je prázdné | proměnnou dej do uvozovek |
| `-eq` použité na text | čísla `-eq`, text `=` |
| chybí `;` před `then` | buď `if ...; then`, nebo `then` na dalším řádku |
| `fi` nebo `done` chybí | každý blok se musí zavřít |
| `while` se nezastaví | uvnitř se nemění proměnná z podmínky |
| `for soubor in *.txt` a žádný `.txt` není | cyklus proběhne jednou s textem `*.txt` |
| `for radek in $(cat soubor)` | rozpadne se na slova, na řádky použij `while read` |
