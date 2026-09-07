## Terminál, shell, příkaz

Tři pojmy, které se pletou.

| Pojem | Co to je |
| --- | --- |
| Terminál | okno, ve kterém píšeš |
| Shell | program, který ten text čte a vykonává |
| Příkaz | program, který shell spustí |

Shell není součást jádra. Je to obyčejná aplikace, kterou jde vyměnit. V Linuxu je nejrozšířenější **bash** - Bourne Again Shell, z roku 1989. Existují i jiné: `sh`, `zsh`, `fish`.

Který používáš, zjistíš takto:

```bash
echo $SHELL
cat /etc/shells
```

## Prompt

Prompt je výzva, kterou shell čeká na příkaz. Text před ním říká, kdo jsi a kde stojíš.

```
student@ubuntu:~/dokumenty$
```

| Část | Co znamená |
| --- | --- |
| `student` | přihlášený uživatel |
| `@` | odděluje uživatele od stroje |
| `ubuntu` | jméno počítače |
| `~/dokumenty` | adresář, ve kterém stojíš - `~` je domovský adresář |
| `$` | jsi běžný uživatel |
| `#` | jsi root, tedy správce |

Rozdíl mezi `$` a `#` si všímej vždycky. Znamená to, že tvůj další příkaz může nenávratně rozbít systém.

Podobu promptu drží proměnná `PS1`:

```bash
echo $PS1
```

## Příkaz, parametry a argumenty

```
ls -l /etc
```

| Část | Název | K čemu je |
| --- | --- | --- |
| `ls` | příkaz | program, který se spustí |
| `-l` | parametr, taky přepínač | mění chování příkazu |
| `/etc` | argument | to, na čem příkaz pracuje |

Parametrů i argumentů může být víc, žádný nebo jen některé. Samotné `ls` funguje taky.

### Krátké a dlouhé parametry

| Zápis | Příklad |
| --- | --- |
| krátký, jedna pomlčka | `ls -l` |
| dlouhý, dvě pomlčky | `ls --all` |
| spojený | `ls -la` je totéž co `ls -l -a` |
| s hodnotou | `head -n 5`, `--color=auto` |

**V Linuxu záleží na velikosti písmen. (casesensitive)** `ls -R` a `ls -r` jsou dva různé parametry.

## Interní a externí příkazy

Ne každý příkaz je program. Část jich je zabudovaná přímo v shellu, ostatní jsou samostatné soubory na disku.

| Typ | Kde je | Příklady |
| --- | --- | --- |
| interní | uvnitř bashe | `cd`, `echo`, `export`, `alias` |
| externí | soubor na disku | `ls`, `cp`, `ping`, `grep` |

Externí příkaz systém hledá v adresářích vypsaných v proměnné `PATH`:

```bash
echo $PATH
which ls
type cd
```

`which` ukáže, který soubor se doopravdy spustí. U interního příkazu nenajde nic - žádný soubor totiž neexistuje. `type` řekne rovnou, o který druh jde.

Proto `man cd` nic nenajde. `cd` není program, takže nemá manuálovou stránku.

## Argumenty s mezerou

Shell rozděluje řádek podle mezer. Jméno souboru, ve kterém je mezera, se proto rozpadne na dva argumenty:

```bash
cd moje dokumenty          nefunguje
cd "moje dokumenty"        funguje
cd moje\ dokumenty         taky funguje
```

Uvozovky drží text pohromadě. Zpětné lomítko zruší význam jednoho následujícího znaku.

## Zástupné znaky

Nemusíš psát celé jméno souboru. **Doplní ho shell**, ne příkaz.

| Znak | Zastoupí | Příklad |
| --- | --- | --- |
| `*` | libovolný počet znaků | `ls *.txt` |
| `?` | právě jeden znak | `ls data?.log` |
| `[abc]` | jeden ze znaků v závorce | `ls [ab]*.txt` |
| `{1..20}` | rozsah | `mkdir den{1..20}` |

```bash
ls *.txt
cp *.txt /tmp
rm rok202?.csv
```

Pozor u mazání. Než pustíš `rm`, vyzkoušej stejný vzor s `ls` - uvidíš přesně, čeho se to týká.

Že rozvíjení dělá shell, si ověříš takhle:

```bash
echo *
```

## Návratový kód

Každý příkaz po sobě nechá číslo. **Nula znamená v pořádku**, cokoli jiného chybu.

```bash
ls /etc
echo $?          vypíše 0

ls /neexistuje
echo $?          vypíše číslo různé od nuly
```

Podle toho se dá řetězit:

```bash
mkdir test && cd test        druhý příkaz jen když první uspěl
cd /nic || echo "nejde"      druhý příkaz jen když první selhal
```

---

# Praktická část

## Instalace Ubuntu ve Windows

Nebudeš potřebovat druhý počítač ani přeinstalovat Windows. Linux poběží uvnitř Windows přes **WSL** - Windows Subsystem for Linux.

### Krok 1 - stáhni Ubuntu

1. Otevři **Microsoft Store**
2. Do vyhledávání napiš `Ubuntu 22.04`
3. Vyber **Ubuntu 22.04.x LTS** a dej **Získat** nebo **Instalovat**

### Krok 2 - první spuštění

Spusť Ubuntu z nabídky Start. Otevře se černé okno s hláškou:

```
Installing, this may take a few minutes...
```

Chvíli to trvá, počkej. Pak se zeptá na uživatelské jméno:

```
Enter new UNIX username:
```

Napiš **malými písmeny, bez mezer a bez diakritiky**. Třeba `student` nebo své křestní jméno.

### Krok 3 - heslo

```
New password:
```

**Při psaní hesla se nic nezobrazuje.** Ani hvězdičky, ani tečky. Kurzor se nehne. To je v pořádku a tak to má být - piš dál a zmáčkni Enter.

Pak ho napíšeš ještě jednou pro kontrolu.

Zvol si heslo, které si **zapamatuješ**. Klidně `infis`. Není to heslo k počítači ani ke školnímu účtu - používá se jen uvnitř Linuxu, když chceš dělat něco jako správce.

Když je hotovo, uvidíš:

```
Installation successful!
student@DESKTOP-XXXX:~$
```

Tenhle prompt znamená, že běžíš v Linuxu.

### Když to nejde

| Problém | Řešení |
| --- | --- |
| Ubuntu se nespustí nebo hlásí chybu | otevři PowerShell **jako správce** a spusť `wsl --install`, pak restartuj počítač |
| Chyba `0x80370102` | ve firmwaru (UEFI) je vypnutá virtualizace, je potřeba ji zapnout |
| Nevím, jestli mi to běží | v PowerShellu spusť `wsl -l -v` |

### Až budeš Linux potřebovat příště

Stačí spustit **Ubuntu** z nabídky Start, nebo v PowerShellu napsat:

```
wsl
```

Heslo se při spuštění nechce. Vyžádá si ho až první příkaz se `sudo`.

## První příkazy

Napiš každý a podívej se, co vypíše. Nic z toho nic nerozbije.

```bash
whoami          pod jakým uživatelem běžíš
hostname        jméno počítače
pwd             kde právě stojíš
id              tvoje číslo a skupiny
date            datum a čas
uname -a        jádro a verze systému
lsb_release -a  distribuce a verze
clear           vyčistí obrazovku
```

## Pohyb v adresářové struktuře

### Kde jsem a co tu je

```bash
pwd             vypíše cestu k aktuálnímu adresáři
ls              vypíše obsah
ls -l           s právy, vlastníkem a velikostí
ls -a           i skryté soubory (začínají tečkou)
ls -la          obojí najednou
ls -lh          velikosti čitelně, v kB a MB
```

### Přesun

```bash
cd /etc         přejde do adresáře /etc
cd ..           o úroveň výš
cd ~            domů
cd              taky domů, bez argumentu
cd -            zpátky tam, kde jsi byl předtím
cd /            do kořene
```

### Absolutní a relativní cesta

| Typ | Začíná | Znamená |
| --- | --- | --- |
| absolutní | `/` | cesta od kořene, platí odkudkoli |
| relativní | čímkoli jiným | cesta od místa, kde právě stojíš |

```bash
cd /var/log              absolutní - funguje odkudkoli
cd dokumenty/fotky       relativní - funguje jen když jsou tady
cd ../..                 o dvě úrovně výš
```

| Zkratka | Znamená |
| --- | --- |
| `.` | tento adresář |
| `..` | nadřazený adresář |
| `~` | domovský adresář |
| `-` | předchozí adresář |

## Ať toho nemusíš tolik psát


| Klávesa | Co udělá |
| --- | --- |
| <kbd>Tab</kbd> | doplní rozepsané jméno souboru nebo příkazu |
| <kbd>Tab</kbd> <kbd>Tab</kbd> | vypíše všechny možnosti, když je jich víc |
| <kbd>↑</kbd> <kbd>↓</kbd> | prochází předchozí příkazy |
| <kbd>Ctrl</kbd>+<kbd>R</kbd> | hledá v historii, piš část příkazu |
| <kbd>Ctrl</kbd>+<kbd>C</kbd> | přeruší běžící příkaz |
| <kbd>Ctrl</kbd>+<kbd>L</kbd> | vyčistí obrazovku |

Zkus napsat `cd /et` a zmáčknout <kbd>Tab</kbd>. Doplní se `/etc/`.

Zkus napsat `cd /u` a zmáčknout <kbd>Tab</kbd> dvakrát. Vypíše všechny možnosti, protože jich je víc.

Historie:

```bash
history          vypíše, co jsi psal
history | tail   posledních pár řádků
!!               zopakuje poslední příkaz
!123             zopakuje příkaz číslo 123
```

## Když si nevíš rady

Skoro každý příkaz umí nápovědu:

```bash
ls --help
```

Podrobněji je to v manuálu. **Ukončíš ho klávesou <kbd>q</kbd>**, hledá se lomítkem:

```bash
man ls
```

Když neznáš ani jméno příkazu:

```bash
apropos user
```
