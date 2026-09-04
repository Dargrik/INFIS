## Interní a externí příkazy

Ne každý příkaz je program. Část jich je zabudovaná přímo v shellu, ostatní jsou samostatné soubory na disku.

| Typ | Kde je | Příklady |
| --- | --- | --- |
| interní | uvnitř `cmd.exe` | `dir`, `cd`, `copy`, `echo`, `cls`, `type` |
| externí | soubor `.exe` na disku | `ping`, `ipconfig`, `whoami`, `tasklist` |

Externí příkaz systém hledá v adresářích vypsaných v proměnné `PATH`:

```
echo %PATH%
where ping
```

`where` ukáže, který soubor se doopravdy spustí. U interního příkazu nenajde nic — žádný soubor totiž neexistuje.

Proto se `dir` chová stejně na každém Windows, ale `ping` jde smazat nebo nahradit.

## Parametry v cmd

Parametr se ve Windows píše s lomítkem a nezáleží na velikosti písmen.

```
dir /b
dir /B
```

Parametrů může být víc za sebou:

```
dir /a /s C:\Windows
```

Na rozdíl od Linuxu **se nedají spojovat** — `/as` neznamená `/a /s`, ale jeden neznámý parametr.

### Parametry s hodnotou

Některé parametry samy o sobě nestačí a čekají za dvojtečkou hodnotu.

| Zápis | Co udělá |
| --- | --- |
| `dir /a:h` | vypíše jen skryté soubory |
| `dir /a:d` | vypíše jen adresáře |
| `dir /o:n` | seřadí podle jména |
| `dir /o:-s` | seřadí podle velikosti sestupně |

Mezera mezi parametrem a hodnotou být nesmí. `dir /a: h` skončí chybou.

## Argumenty s mezerou

Shell rozděluje řádek podle mezer. Cesta, ve které je mezera, se proto rozpadne na dva argumenty:

```
cd C:\Program Files          nefunguje
cd "C:\Program Files"        funguje
```

Uvozovky drží text pohromadě. Používej je vždy, když si nejsi jistý.

## Zástupné znaky

Nemusíš psát celé jméno souboru. Shell si ho doplní sám.

| Znak | Zastoupí | Příklad |
| --- | --- | --- |
| `*` | libovolný počet znaků | `dir *.txt` |
| `?` | právě jeden znak | `dir data?.log` |

```
dir *.txt
copy *.txt C:\zaloha
del rok202?.csv
```

Pozor u mazání. Než pustíš `del`, vyzkoušej stejný vzor s `dir` — uvidíš přesně, čeho se to týká.

## Parametry v PowerShellu

PowerShell je píše s pomlčkou a jménem. Je to delší, ale čitelné.

```
Get-ChildItem -Path C:\Windows -Recurse
```

| Druh | Jak se pozná | Příklad |
| --- | --- | --- |
| s hodnotou | za jménem následuje hodnota | `-Path C:\Windows` |
| přepínač | žádnou hodnotu nemá, jen se uvede | `-Recurse`, `-Force` |
| poziční | jméno se dá vynechat | `Get-ChildItem C:\Windows` |

Jména jdou zkracovat, dokud jsou jednoznačná:

```
Get-ChildItem -Rec
```

Nejrychlejší je psát je tabulátorem. Napiš `-` a mačkej **Tab** — PowerShell nabízí jen parametry, které daná rutina opravdu má.

## Proč to není stejné

| | cmd | PowerShell | bash |
| --- | --- | --- | --- |
| Značka parametru | `/b` | `-Recurse` | `-l`, `--all` |
| Velikost písmen | nezáleží | nezáleží | **záleží** |
| Spojování krátkých | ne | ne | ano (`-la`) |
| Hodnota parametru | `/a:h` | `-Path C:\` | `--color=auto` |

Jeden a tentýž přepínač tedy v jiném shellu vypadá jinak nebo neexistuje. Návod z internetu je vždy potřeba přečíst i s tím, pro jaký shell je psaný.

## Jak poznat, že příkaz selhal

Každý příkaz po sobě nechá návratový kód. Nula znamená v pořádku, cokoli jiného chybu.

```
ping neexistuje.local
echo %ERRORLEVEL%
```

V PowerShellu je totéž v proměnné `$?`, která má hodnotu `True` nebo `False`.

Ve skriptech se podle toho rozhoduje, jestli má pokračovat další krok.
