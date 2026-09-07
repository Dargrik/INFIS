## Jeden strom, žádné jednotky

Linux nemá `C:` ani `D:`. Všechno visí na jediném stromě, který začíná v kořenovém adresáři `/`.

Když připojíš flashku, neobjeví se jako nová jednotka. Připojí se do některého adresáře, třeba `/media/student/USB`, a od té chvíle je součástí téhož stromu.

```bash
ls /
```

Uspořádání není libovolné. Řídí se standardem **FHS**, Filesystem Hierarchy Standard, takže na Ubuntu i na Fedoře najdeš věci na stejných místech.

## Hlavní adresáře

| Adresář | Co v něm je |
| --- | --- |
| `/bin`, `/usr/bin` | programy dostupné všem uživatelům |
| `/sbin`, `/usr/sbin` | programy pro správu systému |
| `/etc` | konfigurační soubory, textové |
| `/home` | domovské adresáře uživatelů |
| `/root` | domovský adresář správce |
| `/var` | data, která se mění za běhu, hlavně logy |
| `/tmp` | dočasné soubory, po restartu se mažou |
| `/dev` | zařízení, disky, terminály |
| `/proc`, `/sys` | okno do běžícího jádra, na disku nic není |
| `/boot` | jádro a zavaděč |
| `/lib`, `/usr/lib` | sdílené knihovny |
| `/opt` | software mimo balíčkovací systém |
| `/mnt`, `/media` | připojené disky a flashky |

Zapamatuj si čtyři nejdůležitější: **`/etc` konfigurace, `/var` logy, `/home` tvoje data, `/tmp` odpad.**

### /etc

Všechna nastavení systému, výhradně v textových souborech. Žádný registr, žádná databáze. Konfiguraci proto jde přečíst, zkopírovat, poslat kolegovi a verzovat.

```bash
ls /etc | head -20
cat /etc/os-release
cat /etc/passwd
```

### /var

Data, která během provozu rostou. Nejčastěji sem chodíš pro logy.

```bash
ls /var
ls /var/log
```

Když serveru dojde místo, je to v devíti případech z deseti kvůli `/var/log`.

### /proc

Zvláštní adresář. Nejsou v něm žádné soubory na disku, jádro ho vytváří v paměti a ukazuje v něm svůj vlastní stav.

```bash
cat /proc/cpuinfo
cat /proc/meminfo
ls /proc
```

Ta čísla, která tam vidíš, jsou čísla běžících procesů.

## Domovský adresář

Tvoje data patří do `/home/jmeno`. Zkratka je `~`.

```bash
cd ~
pwd
ls -la
```

Ve výpisu s `-a` uvidíš spoustu položek začínajících tečkou. **Tečka na začátku jména znamená skrytý soubor**, nic víc. Není v tom žádný atribut jako ve Windows, je to prostě jméno.

| Soubor | K čemu je |
| --- | --- |
| `.bashrc` | nastavení shellu, aliasy, proměnné |
| `.bash_history` | historie příkazů |
| `.ssh/` | klíče pro vzdálený přístup |
| `.config/` | nastavení aplikací |

Programy si ukládají nastavení sem, takže když si přeneseš domovský adresář na jiný počítač, přenese se i všechno tvoje nastavení.

## Kde jsou soubory Windows

Ve WSL najdeš disky Windows připojené pod `/mnt`:

```bash
ls /mnt/c
ls /mnt/c/Users
```

**Pracuj ale v `~`, ne v `/mnt/c`.** Na windowsových souborech nefungují linuxová oprávnění a je to výrazně pomalejší.

## Práce se soubory a adresáři

### Vytvoření

```bash
mkdir pokus              vytvoří adresář
mkdir -p a/b/c           vytvoří i mezilehlé adresáře
touch soubor.txt         vytvoří prázdný soubor
```

### Kopírování a přesun

```bash
cp soubor.txt kopie.txt          zkopíruje soubor
cp -r slozka/ zaloha/            zkopíruje adresář i s obsahem
cp -a slozka/ zaloha/            zachová práva a časy
mv soubor.txt /tmp/              přesune
mv staryNazev.txt novy.txt       přejmenuje
```

`mv` je přesun i přejmenování. Je to jeden příkaz, protože přejmenování je z pohledu systému totéž co přesun v rámci adresáře.

### Mazání

```bash
rm soubor.txt            smaže soubor
rm -r slozka/            smaže adresář i s obsahem
rm -i soubor.txt         zeptá se před smazáním
rmdir prazdna/           smaže jen prázdný adresář
```

**`rm` nemá koš.** Co smažeš, je pryč. Dokud si nejsi jistý, používej `rm -i`.

### Prohlížení obsahu

```bash
cat soubor.txt           vypíše celý soubor
less soubor.txt          listuje, ukončíš klávesou q
head -5 soubor.txt       prvních 5 řádků
tail -5 soubor.txt       posledních 5 řádků
tail -f /var/log/syslog  sleduje soubor a dopisuje nové řádky
wc -l soubor.txt         spočítá řádky
```

### Co to vlastně je

```bash
file /bin/ls             zjistí typ obsahu
stat soubor.txt          velikost, časy, vlastník
du -sh slozka/           kolik místa zabírá
```

**Přípona v Linuxu nic neznamená.** Typ souboru určuje jeho obsah, ne jméno. Proto existuje `file`.

## Editace textu

```bash
nano /etc/hosts
```

Nano je nejjednodušší editor. Dole má nápovědu, `^` znamená <kbd>Ctrl</kbd>.

| Zkratka | Co udělá |
| --- | --- |
| <kbd>Ctrl</kbd>+<kbd>O</kbd> | uloží |
| <kbd>Ctrl</kbd>+<kbd>X</kbd> | ukončí |
| <kbd>Ctrl</kbd>+<kbd>W</kbd> | hledá |

Na cizím serveru občas nano není a musíš do **vim**. Stačí ti pět zkratek, abys z něj vyvázl:

| Klávesa | Co udělá |
| --- | --- |
| <kbd>i</kbd> | přepne do psaní |
| <kbd>Esc</kbd> | zpět do příkazového režimu |
| `:w` | uloží |
| `:q` | ukončí |
| `:q!` | ukončí bez uložení |

## Hledání

```bash
find /etc -name "*.conf"           najde soubory podle jména
find . -type d                     jen adresáře
find . -size +1M                   větší než 1 MB
find . -mtime -7                   změněné za posledních 7 dní
```

Masku dávej do uvozovek, jinak ji rozvine shell dřív, než se k ní `find` dostane.
