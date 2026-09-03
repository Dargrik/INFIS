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
| `~/dokumenty` | adresář, ve kterém stojíš — `~` je domovský adresář |
| `$` | jsi běžný uživatel |
| `#` | jsi root, tedy správce |

Podobu promptu drží proměnná `PS1`. Výpis lze příkazem `echo $PS1`.

## Příkaz, parametry a argumenty

```
ls -l /etc
```

| Část | Název | K čemu je |
| --- | --- | --- |
| `ls` | příkaz | program, který se spustí |
| `-l` | parametr, taky přepínač | mění chování příkazu |
| `/etc` | argument | to, na čem příkaz pracuje |

Krátké parametry mají jednu pomlčku a dají se spojovat — `ls -la` je totéž co `ls -l -a`. Dlouhé mají dvě pomlčky, třeba `ls --all`.

Parametrů i argumentů může být víc, žádný nebo jen některé. Samotné `ls` funguje taky.

## Kdo jsem a kde jsem

| Příkaz | Co vypíše |
| --- | --- |
| `whoami` | pod jakým uživatelem jsi přihlášený |
| `hostname` | jméno počítače |
| `pwd` | adresář, ve kterém právě stojíš |
| `id` | tvoje číslo uživatele a skupiny, do kterých patříš |
| `date` | datum a čas |
| `uname -a` | jádro a verze systému |

## Pohyb a výpis

| Příkaz | Co udělá |
| --- | --- |
| `ls` | vypíše obsah aktuálního adresáře |
| `ls -l` | totéž, ale s právy, vlastníkem a velikostí |
| `ls -a` | ukáže i skryté soubory (začínají tečkou) |
| `ls -la` | obojí najednou |
| `cd /etc` | přejde do adresáře `/etc` |
| `cd ..` | o úroveň výš |
| `cd ~` | domů |
| `cd -` | zpátky tam, kde jsi byl předtím |
