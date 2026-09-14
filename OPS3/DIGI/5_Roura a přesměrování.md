## Tři proudy

Každý program má od začátku otevřené tři kanály.

| Číslo | Název | Odkud nebo kam | Normálně |
| --- | --- | --- | --- |
| `0` | stdin | vstup | klávesnice |
| `1` | stdout | běžný výstup | obrazovka |
| `2` | stderr | chybové hlášky | obrazovka |

Výstup a chyby jdou oba na obrazovku, takže vypadají stejně. Jsou to ale **dva různé kanály** a dají se poslat každý jinam.

```bash
ls /etc /neexistuje
```

Výpis `/etc` přišel přes stdout, hláška o chybě přes stderr.

## Přesměrování výstupu

| Zápis | Co udělá |
| --- | --- |
| `> soubor` | výstup do souboru, **přepíše** ho |
| `>> soubor` | výstup na konec souboru |
| `2> soubor` | chyby do souboru |
| `2>> soubor` | chyby na konec souboru |
| `&> soubor` | výstup i chyby do jednoho souboru |
| `> soubor 2>&1` | totéž, starší zápis |
| `2> /dev/null` | chyby zahodí |

```bash
ls /etc > seznam.txt
date >> log.txt
date >> log.txt
cat log.txt
```

**`>` soubor bez varování přepíše.** Když chceš přidávat, musí tam být `>>`.

### Oddělit výstup od chyb

```bash
ls /etc /neexistuje > vystup.txt 2> chyby.txt
cat vystup.txt
cat chyby.txt
```

Nejčastější použití je zahodit chyby, které tě nezajímají:

```bash
find / -name "*.conf"                 zaplaví tě Permission denied
find / -name "*.conf" 2> /dev/null    jen výsledky
```

`/dev/null` je speciální soubor, který všechno pohltí. Říká se mu černá díra.

### Proč `2>&1` a co znamená

`2>&1` říká: pošli stderr tam, kam právě teď míří stdout. Proto **záleží na pořadí**:

```bash
ls /etc /nic > vse.txt 2>&1     obojí v souboru
ls /etc /nic 2>&1 > vse.txt     chyba na obrazovce, výpis v souboru
```

Ve druhém řádku se stderr přesměroval na obrazovku ještě předtím, než se stdout přesunul do souboru.

## Přesměrování vstupu

```bash
wc -l < /etc/passwd
sort < jmena.txt
```

Program čte ze souboru, jako by ho někdo psal na klávesnici.

## Roura

Roura `|` pošle výstup jednoho příkazu na vstup dalšího. Nevzniká žádný soubor.

```bash
ls /usr/bin | wc -l
history | tail -20
cat /etc/passwd | grep student
```

Příkazy se dají řetězit libovolně dlouho. Každý dělá jednu věc a posílá výsledek dál.

```bash
ls -l /usr/bin | sort -k5 -n | tail -5
```

Vypíše pět největších souborů v `/usr/bin`.

**Rourou teče jen stdout.** Chyby jdou dál na obrazovku. Když je chceš poslat taky, použij `|&`.

### tee

`tee` pošle data dál rourou a zároveň je uloží do souboru. Jako T-kus na potrubí.

```bash
ls /etc | tee seznam.txt | wc -l
```

## Filtry

Filtr je program, který čte vstup, něco s ním udělá a pošle ho dál.

### head, tail, wc

```bash
head -3 /etc/passwd           první 3 řádky
tail -3 /etc/passwd           poslední 3 řádky
tail -n +2 soubor.csv         všechno od 2. řádku, zahodí hlavičku
wc -l /etc/passwd             řádky
wc -w soubor.txt              slova
wc -c soubor.txt              bajty
```

### grep - hledání řádků

```bash
grep bash /etc/passwd         řádky, které obsahují bash
grep -i BASH /etc/passwd      bez ohledu na velikost písmen
grep -v bash /etc/passwd      řádky, které bash NEobsahují
grep -n bash /etc/passwd      i s čísly řádků
grep -c bash /etc/passwd      jen počet
grep -w in soubor.txt         jen celé slovo
grep -r "alias" ~             rekurzivně v adresáři
```

| Vzor | Znamená |
| --- | --- |
| `^text` | řádek začíná |
| `text$` | řádek končí |
| `.` | jeden libovolný znak |
| `[0-9]` | jedna číslice |
| `a.*b` | `a`, cokoli, `b` |

```bash
grep '^root' /etc/passwd
grep 'nologin$' /etc/passwd
```

Vzory dávej do jednoduchých uvozovek, ať je shell nezačne rozvíjet.

### cut - vyříznutí sloupců

```bash
cut -d: -f1 /etc/passwd       první sloupec, oddělovač dvojtečka
cut -d: -f1,7 /etc/passwd     první a sedmý
cut -d, -f2 data.csv          druhý sloupec z CSV
cut -c1-5 soubor.txt          prvních 5 znaků každého řádku
```

### sort - řazení

```bash
sort jmena.txt                abecedně
sort -r jmena.txt             obráceně
sort -n cisla.txt             číselně
sort -t, -k3 -n data.csv      podle 3. sloupce, oddělovač čárka
sort -h                       podle velikosti 1K, 5M, 2G
sort -u jmena.txt             bez duplicit
```

Bez `-n` se čísla řadí jako text. Pak je `10` před `9`, protože `1` je před `9`.

### uniq - duplicity

```bash
sort jmena.txt | uniq         odstraní opakování
sort jmena.txt | uniq -c      spočítá opakování
sort jmena.txt | uniq -d      jen ty, které se opakují
```

**`uniq` porovnává jen sousední řádky.** Vždycky před ním musí být `sort`.

### tr - záměna znaků

```bash
echo "ahoj" | tr a-z A-Z      AHOJ
echo "a:b:c" | tr ':' '\n'    každé na nový řádek
echo "a  b   c" | tr -s ' '   sloučí opakované mezery
```

### Nejčastější kombinace

```bash
sort | uniq -c | sort -rn | head
```

Spočítá, kolikrát se co opakuje, a vypíše nejčastější. Tohle jednou použiješ na log a ušetříš hodinu.
