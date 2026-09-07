## Terminál, shell, příkaz

Tři pojmy, které se pletou.

| Pojem | Co to je |
| --- | --- |
| Terminál | okno, ve kterém píšeš |
| Shell | program, který ten text čte a vykonává |
| Příkaz | program, který shell spustí |

Shell není součást systému. Je to obyčejná aplikace, kterou jde vyměnit. Ve Windows máš dva: **cmd** a **PowerShell**. Okno, ve kterém běží, může být staré okno konzole nebo **Windows Terminal**.

### Jak otevřít příkazovou řádku

1. Nabídka Start, napsat `cmd` nebo `powershell`
2. <kbd>Win</kbd>+<kbd>R</kbd>, napsat `cmd`
3. **V Průzkumníku napsat `cmd` do adresního řádku** - otevře se rovnou ve složce, kde stojíš

Ten třetí způsob zná málokdo a ušetří nejvíc práce.

## Prompt

Prompt je výzva, kterou shell čeká na příkaz. Text před ním říká, kde stojíš.

```
C:\Users\student>
```

| Část | Co znamená |
| --- | --- |
| `C:` | jednotka, na které jsi |
| `\Users\student` | složka, ve které stojíš |
| `>` | konec výzvy, za něj píšeš příkaz |

V PowerShellu vypadá prompt takto:

```
PS C:\Users\student>
```

Windows na rozdíl od Linuxu nemá pro správce jiný znak. Že jsi spustil řádku jako správce, poznáš podle **titulku okna** - je v něm „Správce".

## Příkaz, parametry a argumenty

```
dir /b C:\Windows
```

| Část | Název | K čemu je |
| --- | --- | --- |
| `dir` | příkaz | program, který se spustí |
| `/b` | parametr, taky přepínač | mění chování příkazu |
| `C:\Windows` | argument | to, na čem příkaz pracuje |

Parametrů i argumentů může být víc, žádný nebo jen některé. Samotné `dir` funguje taky.

Ve Windows se parametry píšou s **lomítkem**. Proto se cesty naopak píšou zpětným lomítkem - kdyby se používalo obyčejné, systém by nepoznal, kde končí cesta a začíná parametr.

### Parametry v cmd

Nezáleží na velikosti písmen a **nedají se spojovat**.

```
dir /a /s C:\Windows       správně
dir /as C:\Windows         chyba, /as je neznámý parametr
```

Některé čekají za dvojtečkou hodnotu. Mezera mezi nimi být nesmí.

| Zápis | Co udělá |
| --- | --- |
| `dir /a:h` | vypíše jen skryté soubory |
| `dir /a:d` | vypíše jen adresáře |
| `dir /o:n` | seřadí podle jména |
| `dir /o:-s` | seřadí podle velikosti sestupně |

### Parametry v PowerShellu

Píšou se s pomlčkou a jménem. Je to delší, ale čitelné.

```powershell
Get-ChildItem -Path C:\Windows -Recurse
```

| Druh | Jak se pozná | Příklad |
| --- | --- | --- |
| s hodnotou | za jménem následuje hodnota | `-Path C:\Windows` |
| přepínač | žádnou hodnotu nemá | `-Recurse`, `-Force` |
| poziční | jméno se dá vynechat | `Get-ChildItem C:\Windows` |

Jména jdou zkracovat, dokud jsou jednoznačná. Nejrychlejší je napsat `-` a mačkat <kbd>Tab</kbd> - PowerShell nabízí jen parametry, které daná rutina opravdu má.

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

`where` ukáže, který soubor se doopravdy spustí. U interního příkazu nenajde nic - žádný soubor totiž neexistuje.

Proto se `dir` chová stejně na každém Windows, ale `ping` jde smazat nebo nahradit.

## Argumenty s mezerou

Shell rozděluje řádek podle mezer. Cesta, ve které je mezera, se proto rozpadne na dva argumenty:

```
cd C:\Program Files          nefunguje
cd "C:\Program Files"        funguje
```

Uvozovky drží text pohromadě. Používej je vždy, když si nejsi jistý.

## Zástupné znaky

Nemusíš psát celé jméno souboru. **Doplní ho shell**, ne příkaz.

| Znak | Zastoupí | Příklad |
| --- | --- | --- |
| `*` | libovolný počet znaků | `dir *.txt` |
| `?` | právě jeden znak | `dir data?.log` |

```
dir *.txt
copy *.txt C:\zaloha
del rok202?.csv
```

Pozor u mazání. Než pustíš `del`, vyzkoušej stejný vzor s `dir` - uvidíš přesně, čeho se to týká.

## Návratový kód

Každý příkaz po sobě nechá číslo. **Nula znamená v pořádku**, cokoli jiného chybu.

```
ping neexistuje.local
echo %ERRORLEVEL%
```

V PowerShellu je totéž v proměnné `$?`, která má hodnotu `True` nebo `False`. Ve skriptech se podle toho rozhoduje, jestli má pokračovat další krok.

---

# Praktická část

## cmd a PowerShell - v čem se opravdu liší

**cmd** vznikl v roce 1987 pro Windows NT jako náhrada příkazového řádku z MS-DOSu. Drží zpětnou kompatibilitu, a proto se skoro nemění. Na správu systému už nestačí.

**PowerShell** vydal Microsoft v roce 2006 právě proto, že `cmd` na hromadnou správu nestačil. **Není to novější cmd. Je to jiný nástroj postavený na jiném principu.**

| | cmd | PowerShell | bash |
| --- | --- | --- | --- |
| Vznik | 1987 | 2006 | 1989 |
| Rourou teče | text | **objekty** | text |
| Kde běží | Windows | Windows, Linux, macOS | Linux, macOS, WSL |
| Skripty | `.bat`, `.cmd` | `.ps1` | `.sh` |
| Zápis parametru | `/b` | `-Recurse` | `-l`, `--all` |
| Spojování krátkých | ne | ne | ano (`-la`) |
| Nápověda | `příkaz /?` | `Get-Help` | `man`, `--help` |
| Velikost písmen | nezáleží | nezáleží | **záleží** |

## Rourou tečou objekty

Tohle je celý rozdíl a stojí za to ho pochopit pořádně.

V `cmd` i v bashi předá roura dalšímu programu **text**. Když z něj chceš velikost souboru, musíš ji vyseknout podle pozice nebo oddělovače. Když se formát výpisu změní, přestane to fungovat.

V PowerShellu předá roura **objekt i s jeho vlastnostmi**. Na velikost se prostě zeptáš jménem.

Stejná úloha třikrát - vypiš soubory, které mají v názvu „log":

```
cmd:         dir | findstr log
bash:        ls | grep log
PowerShell:  Get-ChildItem | Where-Object Name -like "*log*"
```

První dva hledají v textu. Třetí se ptá na vlastnost objektu, a proto se z něj dá rovnou pokračovat dál:

```powershell
Get-ChildItem | Where-Object Name -like "*log*" | Measure-Object Length -Sum
```

V `cmd` by tohle znamenalo počítat znaky na řádku.

### Co objekt umí

```powershell
Get-Process | Get-Member
```

Vypíše všechny vlastnosti a metody, které objekt má. **Tohle je nejužitečnější rutina v PowerShellu** - když nevíš, na co se dá zeptat, zeptej se objektu.

## PowerShell - jak jsou rutiny pojmenované

Vždycky **Sloveso-Podstatné jméno**. Když víš, co chceš udělat, uhodneš jméno rutiny, kterou jsi nikdy neviděl.

| Sloveso | Co dělá | Příklad |
| --- | --- | --- |
| `Get-` | zjistí | `Get-Process`, `Get-Service` |
| `Set-` | nastaví | `Set-Location` |
| `New-` | vytvoří | `New-Item` |
| `Remove-` | smaže | `Remove-Item` |
| `Start-`, `Stop-` | spustí, zastaví | `Stop-Process` |

Hledání rutiny podle tématu:

```powershell
Get-Command -Noun Service
Get-Command -Verb Get -Noun *Item*
```

Nápověda i s příklady:

```powershell
Get-Help Get-ChildItem -Examples
```

Poprvé si vyžádá stažení nápovědy. Pusť tedy jednou:

```powershell
Update-Help
```

## Pozor na aliasy

V PowerShellu fungují `ls`, `cat`, `cd` a další jména známá z Linuxu. Jsou to jen **přezdívky** pro rutiny.

```powershell
ls          funguje
ls -la      nefunguje
```

`ls` je přezdívka pro `Get-ChildItem` a ten žádný přepínač `-la` nezná. Alias mění jméno, ne parametry.

```powershell
Get-Alias ls
Get-Alias cat
```

V `cmd` naopak `ls` neexistuje vůbec.

## Roura a filtrování

Pět rutin, se kterými se dá dělat skoro všechno.

| Rutina | Co dělá |
| --- | --- |
| `Where-Object` | vybere jen to, co splňuje podmínku |
| `Select-Object` | vybere vlastnosti nebo prvních N položek |
| `Sort-Object` | seřadí |
| `Measure-Object` | spočítá počet, součet, průměr |
| `ForEach-Object` | udělá něco s každou položkou |

```powershell
Get-Process | Where-Object CPU -gt 10
Get-Process | Sort-Object WS -Descending | Select-Object -First 5
Get-ChildItem C:\Windows | Measure-Object Length -Sum
Get-Service | Where-Object Status -eq "Running" | Select-Object Name, Status
```

Výstup se dá poslat do souboru:

```powershell
Get-Service | Export-Csv sluzby.csv -NoTypeInformation
```

A zobrazit jinak:

```powershell
Get-Process | Format-Table -AutoSize
Get-Process notepad | Format-List
```

## Pohyb v adresářové struktuře

### Kde jsem a co tu je

```
cmd                          PowerShell
------------------------     ------------------------
cd                           Get-Location
dir                          Get-ChildItem
dir /b                       Get-ChildItem -Name
dir /a                       Get-ChildItem -Force
dir /s                       Get-ChildItem -Recurse
tree                         tree
```

V `cmd` **`cd` bez parametru vypíše** aktuální složku. V Linuxu by tě to poslalo domů - je to past, na kterou narazí každý, kdo viděl obojí.

### Přesun

```
cd Dokumenty            přejde do složky
cd ..                   o úroveň výš
cd \                    do kořene jednotky
cd %USERPROFILE%        domů
D:                      přepne na jinou jednotku
cd /d D:\data           přepne jednotku i složku najednou
```

Samotné `cd D:\data` z jednotky `C:` **nepřepne**. Musí se použít `cd /d`, nebo napsat `D:` zvlášť.

V PowerShellu je to jednodušší, `Set-Location` zvládne obojí:

```powershell
Set-Location D:\data
cd D:\data
```

### Absolutní a relativní cesta

| Typ | Začíná | Znamená |
| --- | --- | --- |
| absolutní | písmenem jednotky | `C:\Users\student\Documents` |
| relativní | čímkoli jiným | `Documents\fotky` - od místa, kde stojíš |
| síťová (UNC) | `\\` | `\\server\sdileni` |

| Zkratka | Znamená |
| --- | --- |
| `.` | tato složka |
| `..` | nadřazená složka |
| `\` | kořen jednotky |
| `%USERPROFILE%` | domovská složka |

## Ať toho nemusíš tolik psát

| Klávesa | Co udělá |
| --- | --- |
| <kbd>Tab</kbd> | doplní rozepsané jméno souboru nebo složky |
| <kbd>↑</kbd> <kbd>↓</kbd> | prochází předchozí příkazy |
| <kbd>F7</kbd> | vyskočí historii v okně, vybíráš šipkami |
| <kbd>Ctrl</kbd>+<kbd>C</kbd> | přeruší běžící příkaz |
| `cls` | vyčistí obrazovku |

V `cmd` <kbd>Tab</kbd> **cykluje** mezi možnostmi. V PowerShellu i v bashi je vypisuje.

```
doskey /history        historie v cmd
Get-History            historie v PowerShellu
```

## Když se rozsype čeština

Ve výpisech se místo háčků a čárek objeví nesmysly. Konzole čte znaky podle jiné tabulky, než v jaké jsou uložené. Přepneš ji na UTF-8:

```
chcp 65001
```

Platí to jen pro tohle okno. Po zavření se to vrátí zpátky.

## Spouštění skriptů

Skript `.ps1` po dvojkliku nic neudělá. Je to záměr - brání to tomu, abys omylem spustil něco, co ti přišlo mailem.

Zjistit nastavení:

```powershell
Get-ExecutionPolicy
```

Povolit spouštění vlastních skriptů (příkazová řádka jako správce):

```powershell
Set-ExecutionPolicy RemoteSigned
```

Skript se pak spouští s cestou, i když je ve stejné složce:

```powershell
.\skript.ps1
```

## Když si nevíš rady

```
dir /?                 nápověda k příkazu v cmd
help                   seznam vestavěných příkazů cmd
Get-Help dir           nápověda v PowerShellu
Get-Help * -Category Cmdlet | Measure-Object
```
