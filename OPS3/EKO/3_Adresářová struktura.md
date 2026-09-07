## Jednotky

Windows dělí úložiště na jednotky označené písmenem. Každá má vlastní kořen a vlastní strom.

| Písmeno | Obvykle |
| --- | --- |
| `C:` | systémový disk |
| `D:` | druhý disk nebo optická mechanika |
| `Z:` | připojená síťová jednotka |

```
wmic logicaldisk get name, size, freespace
```

V PowerShellu:

```powershell
Get-PSDrive -PSProvider FileSystem
```

Síťové umístění se dá použít i bez písmene, přes cestu **UNC**:

```
\\server\sdileni\dokumenty
```

## Hlavní složky na C:

| Složka | Co v ní je |
| --- | --- |
| `C:\Windows` | samotný systém |
| `C:\Windows\System32` | systémové knihovny a programy |
| `C:\Program Files` | 64bitové aplikace |
| `C:\Program Files (x86)` | 32bitové aplikace |
| `C:\Users` | data uživatelů |
| `C:\ProgramData` | data aplikací společná všem uživatelům |
| `C:\Temp` | dočasné soubory |

### System32 a SysWOW64

Nejmatoucnější dvojice ve Windows.

| Složka | Co obsahuje |
| --- | --- |
| `System32` | **64bitové** soubory |
| `SysWOW64` | **32bitové** soubory |

Ano, obráceně, než by čekal zdravý rozum. Jméno `System32` zůstalo z 32bitových Windows kvůli zpětné kompatibilitě a při přechodu na 64 bitů se nepřejmenovalo, aby nespadly starší programy. **WOW64** znamená Windows on Windows 64, tedy vrstva, která umí spouštět 32bitové aplikace.

Stejný důvod stojí za dvojicí `Program Files` a `Program Files (x86)`.

### ProgramData a AppData

| Složka | Pro koho | Příklad |
| --- | --- | --- |
| `C:\ProgramData` | pro všechny uživatele | licence, sdílená databáze |
| `C:\Users\jmeno\AppData` | jen pro tebe | tvoje nastavení aplikací |

`AppData` má tři podsložky a rozdíl mezi nimi je praktický:

| Podsložka | Co v ní je |
| --- | --- |
| `Roaming` | nastavení, které se v doméně přenáší mezi počítači |
| `Local` | nastavení vázané na tenhle počítač, mezipaměti |
| `LocalLow` | data programů s omezenými právy |

Obě složky jsou **skryté**. Nejrychleji se do nich dostaneš přes proměnnou:

```
cd %APPDATA%
cd %LOCALAPPDATA%
cd %PROGRAMDATA%
```

## Domovská složka

Tvoje data patří do `C:\Users\jmeno`.

| Složka | K čemu je |
| --- | --- |
| `Desktop` | plocha |
| `Documents` | dokumenty |
| `Downloads` | stažené soubory |
| `Pictures`, `Music`, `Videos` | média |
| `AppData` | nastavení aplikací, skryté |

```
cd %USERPROFILE%
dir
dir /a
```

Druhý příkaz ukáže i skryté položky. Uvidíš `AppData` a několik souborů typu `NTUSER.DAT`, ve kterých je uložená tvoje část registru.

## Proměnné prostředí

Místo dlouhých cest se používají proměnné. Fungují všude, i v Průzkumníku.

| Proměnná | Ukazuje na |
| --- | --- |
| `%USERPROFILE%` | `C:\Users\jmeno` |
| `%APPDATA%` | `C:\Users\jmeno\AppData\Roaming` |
| `%LOCALAPPDATA%` | `C:\Users\jmeno\AppData\Local` |
| `%PROGRAMFILES%` | `C:\Program Files` |
| `%PROGRAMDATA%` | `C:\ProgramData` |
| `%WINDIR%` | `C:\Windows` |
| `%TEMP%` | dočasná složka |

```
echo %USERPROFILE%
set                    vypíše všechny
```

V PowerShellu se píšou jinak:

```powershell
$env:USERPROFILE
Get-ChildItem env:
```

## Skryté a systémové soubory

Ve Windows je „skrytý" **atribut souboru**, ne součást jména. Nastavuje se a čte příkazem `attrib`.

```
dir /a:h               vypíše jen skryté
attrib soubor.txt      ukáže atributy
attrib +h soubor.txt   nastaví skrytý
attrib -h soubor.txt   zruší
```

| Písmeno | Atribut |
| --- | --- |
| `H` | skrytý |
| `S` | systémový |
| `R` | jen ke čtení |
| `A` | archivovat |

V Průzkumníku se zobrazení skrytých souborů zapíná v pásu karet, v záložce **Zobrazení**.

## Registr

Kromě souborů má Windows druhou strukturu, kde jsou uložená nastavení: **registr**. Je to stromová databáze, ne textové soubory.

| Větev | Co v ní je |
| --- | --- |
| `HKEY_LOCAL_MACHINE` | nastavení počítače, společné všem |
| `HKEY_CURRENT_USER` | nastavení přihlášeného uživatele |
| `HKEY_CLASSES_ROOT` | přiřazení přípon k programům |

```
regedit
```

Tohle je zásadní rozdíl proti Linuxu, kde je konfigurace v textových souborech v `/etc`. Registr je rychlejší na čtení, ale nedá se přečíst poznámkovým blokem ani poslat kolegovi mailem.

## Práce se soubory a složkami

### Vytvoření

```
md pokus                    vytvoří složku
md a\b\c                    vytvoří i mezilehlé
type nul > soubor.txt       vytvoří prázdný soubor
```

V PowerShellu:

```powershell
New-Item -ItemType Directory pokus
New-Item -ItemType File soubor.txt
```

### Kopírování a přesun

```
copy soubor.txt kopie.txt         zkopíruje soubor
xcopy /s /e slozka cil            zkopíruje složku i s obsahem
robocopy slozka cil /e            totéž, spolehlivěji
move soubor.txt D:\               přesune
ren stary.txt novy.txt            přejmenuje
```

`robocopy` je dnes doporučený nástroj. Umí pokračovat po přerušení, zvládá dlouhé cesty a vypisuje přehled.

### Mazání

```
del soubor.txt             smaže soubor
del /s *.tmp               smaže rekurzivně
rd slozka                  smaže prázdnou složku
rd /s slozka               smaže složku i s obsahem
```

**`del` v příkazové řádce nemá koš.** Co smažeš, je pryč.

### Prohlížení obsahu

```
type soubor.txt            vypíše celý soubor
more soubor.txt            listuje po stránkách
tree                       stromový výpis složek
tree /f                    i se soubory
```

V PowerShellu:

```powershell
Get-Content soubor.txt
Get-Content soubor.txt -Head 5
Get-Content soubor.txt -Tail 5
Get-Content soubor.txt -Wait
```

Poslední varianta sleduje soubor a vypisuje nové řádky, jak přibývají. Hodí se na logy.

## Hledání

```
dir C:\Windows\*.log /s /b
where ping
```

V PowerShellu:

```powershell
Get-ChildItem C:\Windows -Filter *.log -Recurse
Get-ChildItem C:\Windows -Recurse | Where-Object Length -gt 1MB
```