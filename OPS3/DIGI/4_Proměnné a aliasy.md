## Proměnné

Proměnná je pojmenované místo, kam si shell uloží text. Používá se všude: v promptu, ve skriptech, v nastavení programů.

```bash
jmeno=Petr
echo $jmeno
echo "Ahoj $jmeno"
```

**Kolem `=` nesmí být mezera.** `jmeno = Petr` shell pochopí jako příkaz `jmeno` se dvěma argumenty.

| Zápis | Co udělá |
| --- | --- |
| `x=5` | uloží hodnotu |
| `echo $x` | vypíše hodnotu |
| `echo ${x}px` | složené závorky oddělí jméno od okolního textu |
| `unset x` | proměnnou smaže |

Proměnná, která neexistuje, se nahradí prázdným textem. Shell nehlásí chybu, takže překlep v názvu nepoznáš.

### Proměnné prostředí

Obyčejná proměnná platí jen v tomhle shellu. Programy, které z něj spustíš, ji nevidí. Aby ji viděly, musíš ji **exportovat**.

```bash
barva=modra
bash -c 'echo $barva'        nic nevypíše

export barva
bash -c 'echo $barva'        vypíše modra
```

`bash -c` spustí nový shell, provede příkaz a skončí. Je to nejrychlejší způsob, jak ověřit, co vidí spuštěný program.

| Příkaz | Co vypíše |
| --- | --- |
| `env` | proměnné prostředí |
| `set` | všechny proměnné i funkce shellu |
| `printenv HOME` | jednu proměnnou prostředí |

### Proměnné, které už existují

| Proměnná | Obsahuje |
| --- | --- |
| `HOME` | domovský adresář |
| `USER` | přihlášený uživatel |
| `PWD` | aktuální adresář |
| `SHELL` | tvůj výchozí shell |
| `PATH` | kde se hledají programy |
| `LANG` | jazyk a kódování |
| `PS1` | podoba promptu |
| `HISTSIZE` | kolik příkazů si pamatuje historie |

Názvy systémových proměnných se píšou velkými písmeny. Svoje piš malými, ať si je nepřepíšeš.

## PATH

Když napíšeš `ls`, shell neví, kde program je. Prochází adresáře vypsané v `PATH` **zleva doprava** a spustí první, který najde.

```bash
echo $PATH
echo $PATH | tr ':' '\n'      každý adresář na vlastní řádek
which ls
type ls
```

### Proč `./skript` a ne `skript`

Aktuální adresář v `PATH` není. Program, který leží vedle tebe, musíš spustit s cestou:

```bash
./muj_skript.sh
```

Je to bezpečnostní opatření. Kdyby tam aktuální adresář byl, stačilo by, aby ti někdo do složky podstrčil soubor jménem `ls`.

### Vlastní adresář s programy

```bash
mkdir -p ~/bin
export PATH="$HOME/bin:$PATH"
```

Nový adresář dáváš **na začátek** a za něj původní obsah. Kdybys napsal `PATH=~/bin`, přepíšeš celou proměnnou a přestane fungovat skoro všechno. Pomůže zavřít a otevřít terminál.

## Aliasy

Alias je zkratka pro delší příkaz.

```bash
alias                          vypíše všechny aliasy
alias ll='ls -alF'
alias ..='cd ..'
type ll                        řekne, že je to alias
unalias ll                     zruší alias
\ls                            spustí ls bez aliasu
```

Ubuntu má pár aliasů nastavených od začátku, třeba `ll`, `la` a barevné `grep`.

## Aby nastavení vydrželo

Všechno, co nastavíš v terminálu, zmizí, jakmile ho zavřeš. Nastavení, které má platit pořád, patří do souboru **`~/.bashrc`**. Bash ho načte při každém otevření terminálu.

```bash
nano ~/.bashrc
```

Na konec souboru připiš:

```bash
alias ll='ls -alF'
export PATH="$HOME/bin:$PATH"
```

Ulož a načti znovu, bez zavírání terminálu:

```bash
source ~/.bashrc
```

**Než do `.bashrc` sáhneš poprvé, udělej si zálohu:**

```bash
cp ~/.bashrc ~/.bashrc.zaloha
```

## Historie příkazů

| Zápis | Co udělá |
| --- | --- |
| `history` | vypíše historii |
| `!!` | zopakuje poslední příkaz |
| `sudo !!` | zopakuje poslední příkaz jako správce |
| `!$` | poslední argument předchozího příkazu |
| `!42` | příkaz číslo 42 z historie |
| <kbd>Ctrl</kbd>+<kbd>R</kbd> | hledá v historii |

```bash
mkdir ~/projekty/web
cd !$
```

`sudo !!` se hodí, když napíšeš příkaz a on ti odpoví, že nemáš oprávnění.

## Metaznaky

Některé znaky shell nebere jako text, ale jako pokyn.

| Znak | Význam |
| --- | --- |
| mezera | odděluje argumenty |
| `$` | hodnota proměnné |
| `*` `?` `[ ]` `{ }` | zástupné znaky |
| `~` | domovský adresář |
| `\|` `>` `<` | roura a přesměrování |
| `;` `&&` `\|\|` | spojování příkazů |
| `#` | komentář, zbytek řádku se ignoruje |
| `'` `"` `\` | ruší význam ostatních znaků |

## Uvozovky a zpětné lomítko

| Zápis | Co se stane uvnitř |
| --- | --- |
| `'jednoduché'` | nic, všechno je obyčejný text |
| `"dvojité"` | funguje `$`, ostatní znaky jsou text |
| `\` | zruší význam jednoho následujícího znaku |

```bash
echo "Ahoj $USER"             Ahoj student
echo 'Ahoj $USER'             Ahoj $USER
echo "Cena je \$50"           Cena je $50
echo *                        vypíše soubory
echo "*"                      vypíše hvězdičku
```

Soubory s mezerou v názvu:

```bash
touch "muj soubor.txt"
ls muj\ soubor.txt
rm 'muj soubor.txt'
```

Když si nejsi jistý, dávej proměnné do dvojitých uvozovek. `"$soubor"` funguje, i když je v názvu mezera.

## Výstup příkazu jako text

`$( )` spustí příkaz a na jeho místo dosadí to, co vypsal.

```bash
echo "Dnes je $(date +%d.%m.%Y)"
echo "Jsem v $(pwd)"
mkdir zaloha_$(date +%F)
pocet=$(ls | wc -l)
echo "Souborů: $pocet"
```

Výpočty s celými čísly:

```bash
echo $((5 + 3))
echo $((2 ** 10))
```

## Podmíněné spojování příkazů

Každý příkaz vrací návratový kód. `0` je úspěch, cokoli jiného chyba.

| Zápis | Kdy se spustí druhý příkaz |
| --- | --- |
| `a ; b` | vždycky |
| `a && b` | jen když `a` uspěl |
| `a \|\| b` | jen když `a` selhal |

```bash
mkdir test && cd test
cd /neexistuje || echo "Adresář není"
sudo apt update && sudo apt upgrade
```

| Kód | Obvykle znamená |
| --- | --- |
| `0` | v pořádku |
| `1` | obecná chyba |
| `2` | špatně zadaný příkaz |
| `126` | soubor není spustitelný |
| `127` | příkaz nenalezen |
| `130` | přerušeno <kbd>Ctrl</kbd>+<kbd>C</kbd> |

```bash
neexistujiciprikaz
echo $?                       127
```

Jestli soubor existuje, zjistíš příkazem `test`:

```bash
test -f ~/.bashrc && echo "Je tam"
test -d ~/bin || mkdir ~/bin
```