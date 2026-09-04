## Co je shell

Shell je program, který čte, co napíšeš, a spouští to. Není součástí jádra — je to obyčejná aplikace, kterou jde vyměnit.

Nezaměňuj tři věci:

| Pojem | Co to je |
| --- | --- |
| Terminál | okno, ve kterém píšeš |
| Shell | program, který ten text čte a vykonává |
| Příkaz | program, který shell spustí |

Ve Windows je terminálem **Windows Terminal** nebo starší okno konzole, shellem `cmd` nebo PowerShell.

## cmd

Vznikl v roce 1987 pro Windows NT jako náhrada příkazového řádku z MS-DOSu. Zpětnou kompatibilitu drží dodnes, a proto se skoro nemění.

Umí spouštět programy, pracovat se soubory a zpracovávat dávky. Na správu systému už nestačí — nemá přístup k většině moderních rozhraní Windows.

```
dir /b C:\Windows
```

Skripty se ukládají jako `.bat` nebo `.cmd`.

## PowerShell

Microsoft ho vydal v roce 2006, protože `cmd` na hromadnou správu nestačil. Není to vylepšený `cmd` — je to jiný nástroj postavený na jiném principu.

```
Get-ChildItem C:\Windows | Select-Object Name
```

Skripty se ukládají jako `.ps1`.

### Sloveso-Podstatné jméno

Rutiny mají jednotné pojmenování. Když víš, co chceš udělat, uhodneš jméno rutiny, kterou jsi nikdy neviděl.

| Sloveso | Co dělá | Příklad |
| --- | --- | --- |
| `Get-` | zjistí | `Get-Process`, `Get-Service` |
| `Set-` | nastaví | `Set-Service`, `Set-Location` |
| `New-` | vytvoří | `New-Item`, `New-LocalUser` |
| `Remove-` | smaže | `Remove-Item` |
| `Start-`, `Stop-` | spustí, zastaví | `Stop-Process` |

Seznam všeho, co pracuje se službami:

```
Get-Command -Noun Service
```

### Rourou tečou objekty, ne text

Tohle je celý rozdíl proti `cmd` i proti bashi.

V `cmd` i v bashi předá roura dalšímu programu **text**. Když z něj chceš vytáhnout velikost souboru, musíš ji z řádku vyseknout podle pozice nebo oddělovače. Když se výpis změní, přestane to fungovat.

V PowerShellu předá roura **objekt** i s jeho vlastnostmi. Na velikost se prostě zeptáš jménem:

```
Get-ChildItem | Where-Object Length -gt 1MB
```

Co objekt umí, zjistíš takto:

```
Get-Process | Get-Member
```

### Pozor na aliasy

V PowerShellu fungují `ls`, `cat`, `cd` a další jména známá z Linuxu. Jsou to jen přezdívky pro rutiny — **parametry mají úplně jiné**.

```
ls          funguje
ls -la      nefunguje
```

`ls` je přezdívka pro `Get-ChildItem` a ten žádný přepínač `-la` nezná. Proto se v `cmd` `ls` nespustí vůbec a v PowerShellu se chová jinak, než čekáš.

## bash

Shell z prostředí Linuxu a macOS. Jméno je zkratka pro Bourne Again Shell, vznikl v roce 1989 jako svobodná náhrada staršího shellu.

```
ls -la /etc
```

Skripty se ukládají jako `.sh`. Ve Windows se dá spustit přes WSL.

## Stejná úloha třikrát

Vypiš soubory ve složce a vezmi jen ty, které mají v názvu „log".

```
cmd:         dir | findstr log

bash:        ls | grep log

PowerShell:  Get-ChildItem | Where-Object Name -like "*log*"
```

První dva hledají v textu. Třetí se ptá na vlastnost objektu — a proto z něj jde rovnou pokračovat dál, třeba seřadit podle velikosti nebo zjistit součet.
