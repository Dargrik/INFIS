## Prompt

Prompt je výzva, kterou příkazový řádek čeká na příkaz. Text před ním říká, kde stojíš.

```
C:\Users\student>
```

| Část | Co znamená |
| --- | --- |
| `C:` | jednotka, na které jsi |
| `\Users\student` | složka, ve které jsi |
| `>` | konec výzvy, za něj píšeš příkaz |

Windows na rozdíl od Linuxu nemá pro správce jiný znak. Že je spuštěn příkazový řádek jako správce, lze poznat podle titulku okna, kde je v něm "Správce".

## Příkaz, parametry a argumenty

```
dir /b C:\Windows
```

| Část | Název | K čemu je |
| --- | --- | --- |
| `dir` | příkaz | program, který se spustí |
| `/b` | parametr, taky přepínač | mění chování příkazu |
| `C:\Windows` | argument | to, na čem příkaz pracuje |

Ve Windows se parametry píšou s lomítkem. Proto se cesty naopak píšou zpětným lomítkem - kdyby se používalo obyčejné, systém by nepoznal, kde končí cesta a začíná parametr.

Parametrů i argumentů může být víc, žádný nebo jen některé. Samotné `dir` funguje taky.

## Kdo jsem a kde jsem

| Příkaz | Co vypíše |
| --- | --- |
| `whoami` | pod jakým uživatelem jsi přihlášený |
| `hostname` | jméno počítače |
| `cd` | bez parametru vypíše složku, ve které právě stojíš |
| `whoami /groups` | skupiny, do kterých patříš |
| `date /t` a `time /t` | datum a čas |
| `ver` | verze systému |

## Pohyb a výpis

| Příkaz | Co udělá |
| --- | --- |
| `dir` | vypíše obsah aktuální složky, u windows lze také využívat alias `ls` |
| `dir /b` | jen jména, bez datumů a velikostí |
| `dir /a` | ukáže i skryté a systémové soubory |
| `cd Windows` | přejde do složky |
| `cd ..` | o úroveň výš |
| `cd \` | do kořene jednotky |
| `cd %USERPROFILE%` | domů |
| `D:` | přepne na jinou jednotku |
