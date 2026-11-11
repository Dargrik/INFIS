## Dvě různé otázky

Hledat se dá dvojím způsobem a lidi si to často pletou.

| Otázka | Nástroj |
| --- | --- |
| Kde je soubor, který se jmenuje takhle? | `find`, `locate` |
| Ve kterém souboru je napsané tohle? | `grep -r` |

První hledá podle jména a vlastností, druhý podle obsahu.

## find

```bash
find KDE CO_HLEDAT
```

`KDE` je adresář, od kterého se začne. Prochází se i všechny podadresáře.

```bash
find . -name "poznamky.txt"
find /etc -name "*.conf"
find ~ -name "*.pdf"
```

Když vynecháš `KDE`, bere se aktuální adresář. Když vynecháš podmínku, vypíše se úplně všechno.

**Masku vždycky dávej do uvozovek.** Bez nich ji rozvine shell ještě předtím, než se k ní `find` dostane, a dostaneš nesmyslný výsledek.

### Podle jména

```bash
find . -name "*.log"            přesně podle velikosti písmen
find . -iname "*.LOG"           bez ohledu na velikost písmen
find . -name "zaloha*"          začíná na zaloha
```

### Podle typu

```bash
find . -type f                  jen soubory
find . -type d                  jen adresáře
find . -type l                  jen symbolické odkazy
```

### Podle velikosti

```bash
find . -size +1M                větší než 1 MB
find . -size -100k              menší než 100 kB
find /var/log -size +10M        velké logy
```

| Přípona | Jednotka |
| --- | --- |
| `k` | kilobajty |
| `M` | megabajty |
| `G` | gigabajty |
| `c` | bajty |

Znaménko `+` znamená víc než, `-` míň než. Bez znaménka hledá přesnou velikost, což skoro nikdy nechceš.

### Podle času

```bash
find . -mtime -7                změněné za posledních 7 dní
find . -mtime +30               starší než 30 dní
find . -mmin -60                změněné za poslední hodinu
find . -newer soubor.txt        novější než tenhle soubor
```

`mtime` je čas poslední změny obsahu. Pozor na znaménka, jsou stejná jako u velikosti, ale lidem se pletou: `-7` znamená méně než sedm dní staré, tedy nedávné.

### Podle vlastníka a práv

```bash
find /home -user student
find . -perm 777
find . -type f -perm -u+x       soubory spustitelné vlastníkem
```

### Jak hluboko

```bash
find . -maxdepth 1 -type f      jen tenhle adresář, nikam dál
find . -maxdepth 2 -name "*.sh"
```

`-maxdepth 1` je rozdíl mezi "podívej se sem" a "prolez celý disk". U velkých stromů to šetří hodně času.

### Prázdné soubory a adresáře

```bash
find . -empty
find . -type d -empty
```

## Spojování podmínek

Podmínky napsané za sebou platí **všechny zároveň**.

```bash
find . -type f -name "*.log" -size +1M -mtime +30
```

Soubory, které jsou typu f a jmenují se `*.log` a jsou větší než 1 MB a starší než 30 dní.

| Zápis | Znamená |
| --- | --- |
| `podminka1 podminka2` | obě zároveň |
| `podminka1 -o podminka2` | aspoň jedna |
| `! podminka` | naopak |

```bash
find . -name "*.jpg" -o -name "*.png"
find . -type f ! -name "*.txt"
find . \( -name "*.jpg" -o -name "*.png" \) -size +1M
```

Závorky se musí odzadu vyescapovat zpětným lomítkem, jinak je sežere shell.

## Co s nalezenými soubory

Samotný výpis je k ničemu, když jich je tisíc. `find` s nimi umí rovnou něco udělat.

### -delete

```bash
find . -name "*.tmp" -delete
```

**Nejdřív si to pusť bez `-delete`.** Podívej se, co to našlo, a teprve potom mazej. Smazané soubory v Linuxu nejsou v koši.

### -exec

```bash
find . -name "*.log" -exec rm {} \;
find . -name "*.txt" -exec cp {} /zaloha/ \;
find . -name "*.sh" -exec chmod +x {} \;
```

`{}` je místo, kam se dosadí nalezený soubor. `\;` ukončuje příkaz.

Rozdíl mezi `\;` a `+`:

```bash
find . -name "*.txt" -exec wc -l {} \;      spustí wc pro každý soubor zvlášť
find . -name "*.txt" -exec wc -l {} +       spustí wc jednou se všemi soubory
```

Varianta s `+` je mnohem rychlejší. Používej ji všude, kde to příkaz dovolí.

### xargs

```bash
find . -name "*.log" | xargs rm
find . -name "*.txt" | xargs wc -l
```

`xargs` bere řádky ze vstupu a dělá z nich argumenty dalšího příkazu. Dělá v podstatě totéž co `-exec ... +`.

**Problém s mezerami v názvech:**

```bash
find . -name "*.txt" -print0 | xargs -0 rm
```

`-print0` odděluje jména nulovým bajtem místo konce řádku a `-0` to na druhé straně čte. Bez toho ti soubor `moje poznamky.txt` rozpadne na dva argumenty.

## locate

```bash
locate heslo.txt
locate -i readme
locate "*.conf"
```

`locate` je bleskový, protože nehledá na disku. Čte předpřipravenou databázi.

```bash
sudo updatedb
```

Z toho plyne jeho jediná vada: **soubor, který jsi vytvořil před minutou, v databázi ještě není.** Databáze se obnovuje jednou denně nebo ručně.

| | `find` | `locate` |
| --- | --- | --- |
| Rychlost | pomalý | okamžitý |
| Aktuálnost | vždy aktuální | podle poslední databáze |
| Podmínky | velikost, čas, typ, práva | jen jméno |

Když není nainstalovaný: `sudo apt install plocate`.

## Kde je program

```bash
which ls                   cesta ke spustitelnému souboru
type ls                    co to vlastně je
type -a ls                 všechny varianty
whereis ls                 program, zdrojáky i manuál
command -v ls              totéž co which, funguje všude
```

`type` je užitečnější než `which`, protože pozná i alias a vestavěný příkaz:

```bash
type cd        cd is a shell builtin
type ll        ll is aliased to `ls -alF'
type python3   python3 is /usr/bin/python3
```

## Hledání v obsahu souborů

```bash
grep -r "PATH" /etc              rekurzivně celý adresář
grep -rl "PATH" /etc             jen jména souborů
grep -rn "alias" ~               i s čísly řádků
grep -ri "heslo" .               bez ohledu na velikost písmen
```

| Parametr | Co udělá |
| --- | --- |
| `-r` | projde i podadresáře |
| `-l` | vypíše jen jména souborů |
| `-n` | přidá číslo řádku |
| `-i` | ignoruje velikost písmen |
| `--include="*.sh"` | prohledá jen tyhle soubory |
| `--exclude-dir=.git` | tenhle adresář vynechá |

```bash
grep -rn --include="*.sh" "exit" ~
```

Chyby o nepřístupných adresářích zahoď:

```bash
grep -r "neco" /etc 2> /dev/null
```

## Kam se podělo místo

```bash
df -h                           volné místo na discích
du -sh ~                        kolik zabírá domovský adresář
du -h --max-depth=1 ~           po jednotlivých podadresářích
du -h --max-depth=1 ~ | sort -h | tail -10
```

Poslední řádek je nejpoužitelnější. Seřadí podadresáře podle velikosti a ukáže deset největších. `sort -h` rozumí zápisu `1,5G` a `300M`.

Pět největších souborů v adresáři:

```bash
find ~ -type f -printf "%s %p\n" 2> /dev/null | sort -rn | head -5
```

## Kde se to láme

| Chyba | Co se stane |
| --- | --- |
| `find . -name *.log` bez uvozovek | shell masku rozvine a `find` dostane jména souborů |
| `find -name "*.log" .` | pořadí je `find KDE CO`, ne naopak |
| `locate` nic nenajde | soubor vznikl po poslední `updatedb` |
| `-mtime -7` a `+7` naopak | mínus je nedávné, plus je staré |
| `-exec rm {} \;` bez kontroly | smaže víc, než jsi čekal |
| mezery ve jménech a `xargs` | potřebuješ `-print0` a `-0` |
| `find /` bez `-maxdepth` | prolézá celý disk a trvá to minuty |
