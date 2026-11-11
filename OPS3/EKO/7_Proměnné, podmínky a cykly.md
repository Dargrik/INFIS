## Od posloupnosti k programu

Dávkový soubor z minulé lekce dělal pokaždé to samé. Teď ho naučíme dvě věci, které z něj udělají program:

- **podmínku**, aby reagoval na situaci,
- **cyklus**, aby stejnou věc udělal se sto soubory.

## Proměnné

```
@echo off
set JMENO=Novak
set POCET=5

echo Ahoj %JMENO%
echo Souboru: %POCET%
```

Při zápisu bez procent, při čtení v procentech.

**Kolem rovnítka nesmí být mezery.** `set JMENO = Novak` vytvoří proměnnou `JMENO ` s mezerou na konci a hodnotou ` Novak`. Pak `%JMENO%` nenajde nic.

Hodnotu s mezerou dej celou do uvozovek:

```
set "CESTA=C:\Program Files\Moje aplikace"
echo %CESTA%
```

### Výpis a smazání

```
set                  vypíše všechny proměnné
set PATH             vypíše ty, které začínají na PATH
set JMENO=           smaže proměnnou
```

Proměnná platí **jen v tom jednom okně**. Po zavření zmizí. Natrvalo se nastavuje příkazem `setx`, o tom byla řeč v lekci o uživatelské konfiguraci.

### Počítání

```
set /a VYSLEDEK=10+3
set /a VYSLEDEK=%POCET%*2
set /a POCET=%POCET%+1
set /a POCET+=1
```

Bez `/a` se do proměnné uloží text `10+3`, ne číslo 13. Dávkový soubor počítá jen s celými čísly, `10/3` je 3.

### Vstup od uživatele

```
@echo off
set /p JMENO=Jak se jmenujes?
echo Ahoj %JMENO%
```

```
set /p POKRACOVAT=Pokracovat? (a/n)
```

### Užitečné proměnné systému

| Proměnná | Obsahuje |
| --- | --- |
| `%USERNAME%` | přihlášený uživatel |
| `%USERPROFILE%` | `C:\Users\jmeno` |
| `%COMPUTERNAME%` | název počítače |
| `%DATE%` a `%TIME%` | datum a čas |
| `%TEMP%` | dočasná složka |
| `%ERRORLEVEL%` | návratový kód posledního příkazu |

```
echo Report z %COMPUTERNAME% od %USERNAME% > report.txt
```

## Argumenty

Hodnoty se dají skriptu předat už při spuštění. Tím je skript použitelný i pro někoho jiného.

```
@echo off
echo Prvni: %1
echo Druhy: %2
echo Jmeno skriptu: %0
```

```
pokus.bat ahoj svete
Prvni: ahoj
Druhy: svete
```

| Zápis | Znamená |
| --- | --- |
| `%0` | jméno skriptu |
| `%1` až `%9` | jednotlivé argumenty |
| `%*` | všechny najednou |
| `%~1` | argument bez uvozovek |
| `%~dp0` | složka, kde skript leží |

## Podmínky

```
if PODMÍNKA příkaz
```

```
if PODMÍNKA (
    příkazy
) else (
    příkazy
)
```

**Otevírací závorka musí být na stejném řádku jako `if`** a `) else (` musí být na jednom řádku. Jinak cmd ohlásí chybu syntaxe.

### Existuje soubor

```
@echo off
if exist "report.txt" (
    echo Soubor je tady
) else (
    echo Soubor chybi
)
```

```
if not exist "D:\Zalohy" mkdir "D:\Zalohy"
```

U složky je spolehlivější psát `if exist "D:\Zalohy\"` s lomítkem na konci.

### Porovnání textu

```
if "%1"=="start" echo Spoustim
if /i "%1"=="START" echo Spoustim
```

| Zápis | Znamená |
| --- | --- |
| `==` | shodné |
| `/i` | nezáleží na velikosti písmen |
| `not` | naopak |

**Proměnné v podmínce dávej do uvozovek.** Když je `%1` prázdné, `if %1==start` se rozpadne na `if ==start` a skript spadne. S uvozovkami vyjde `if ""=="start"` a to je jen nepravda.

Kontrola chybějícího argumentu:

```
@echo off
if "%1"=="" (
    echo Pouziti: %0 ADRESAR
    exit /b 1
)
```

`exit /b 1` ukončí skript s kódem 1. Bez `/b` by se zavřelo celé okno cmd.

### Porovnání čísel

```
if %POCET% GTR 100 echo Pres sto
if %POCET% LEQ 10 echo Malo
```

| Zápis | Znamená |
| --- | --- |
| `EQU` | rovná se |
| `NEQ` | nerovná se |
| `GTR` | větší než |
| `GEQ` | větší nebo rovno |
| `LSS` | menší než |
| `LEQ` | menší nebo rovno |

### Podle návratového kódu

```
@echo off
ping -n 1 seznam.cz > nul

if %ERRORLEVEL% EQU 0 (
    echo Internet funguje
) else (
    echo Internet nefunguje
)
```

Starší zápis `if errorlevel 1` znamená "kód je 1 **nebo vyšší**", což překvapí. Piš raději `if %ERRORLEVEL% EQU 1`.

## Cyklus for

Dávkový soubor má jediný cyklus, zato v několika podobách. Proměnná cyklu se v souboru píše se **dvěma** procenty.

```
for %%i in (SEZNAM) do příkaz
```

### Přes seznam

```
@echo off
for %%i in (jedna dve tri) do echo %%i
```

### Přes soubory

```
for %%s in (*.txt) do echo Soubor: %%s
for %%s in (*.log) do del "%%s"
```

Rekurzivně i v podsložkách:

```
for /r %%s in (*.tmp) do del "%%s"
for /r "C:\Data" %%s in (*.docx) do echo %%s
```

Jen složky:

```
for /d %%a in (C:\Users\*) do echo %%a
```

### Čísla

```
for /l %%i in (1,1,10) do echo Kolo %%i
for /l %%i in (0,5,100) do echo %%i
```

Závorka je `(od, krok, do)`.

### Po řádcích ze souboru

```
for /f "tokens=*" %%r in (seznam.txt) do echo Radek: %%r
```

Rozdělení do sloupců:

```
for /f "tokens=1,3 delims=;" %%a in (data.csv) do echo %%a a %%b
```

| Volba | Význam |
| --- | --- |
| `tokens=1,3` | první a třetí sloupec |
| `tokens=*` | celý řádek |
| `delims=;` | oddělovač |
| `skip=1` | přeskočí hlavičku |

První převzatý sloupec se uloží do `%%a`, další postupně do `%%b`, `%%c`. Písmena nemusíš nikde deklarovat.

Nad výstupem příkazu:

```
for /f "tokens=2 delims=:" %%a in ('ipconfig ^| findstr /C:"IPv4"') do echo IP: %%a
```

Svislítko uvnitř apostrofů se musí vyescapovat stříškou jako `^|`.

### Dvě procenta, nebo jedno

| Kde | Zápis |
| --- | --- |
| v dávkovém souboru | `%%i` |
| přímo v okně cmd | `%i` |

Když si cyklus vyzkoušíš v okně a pak ho zkopíruješ do souboru, musíš procenta zdvojit. Jinak dostaneš hlášku, že `%i` bylo nečekané.

## Skoky a návěští

```
@echo off
if "%1"=="" goto :konec

echo Pracuji s %1
goto :hotovo

:konec
echo Chybi argument

:hotovo
echo Konec
```

Návěští se píše s dvojtečkou na začátku řádku. `goto :eof` skočí na konec souboru.

Skoky používej střídmě. Skript plný `goto` se po měsíci nedá přečíst.

## Záludnost: proměnná uvnitř cyklu

```
@echo off
set POCET=0
for %%s in (*.txt) do (
    set /a POCET=%POCET%+1
    echo Zatim: %POCET%
)
echo Celkem: %POCET%
```

Tohle **nefunguje** a vypisuje pořád nulu. Cmd dosadí hodnoty proměnných do celého bloku ještě předtím, než ho začne provádět.

Řešení:

```
@echo off
setlocal enabledelayedexpansion
set POCET=0
for %%s in (*.txt) do (
    set /a POCET=!POCET!+1
    echo Zatim: !POCET!
)
echo Celkem: %POCET%
endlocal
```

Uvnitř bloku se píšou vykřičníky místo procent a nahoře musí být `setlocal enabledelayedexpansion`.

Tohle je nejčastější důvod, proč lidé nad dávkovými soubory zoufají. Když na to narazíš, víš, co s tím.

## Celý příklad

```
@echo off
setlocal enabledelayedexpansion
cd /d "%~dp0"

rem uklid.bat - smaze stare logy ve slozce
rem Pouziti: uklid.bat SLOZKA

if "%1"=="" (
    echo Pouziti: %0 SLOZKA
    exit /b 1
)

if not exist "%~1\" (
    echo Slozka %1 neexistuje
    exit /b 2
)

set POCET=0

for %%s in ("%~1\*.log") do (
    echo Mazu %%~nxs
    del "%%s"
    set /a POCET=!POCET!+1
)

echo.
echo Smazano souboru: !POCET!
endlocal
pause
```

| Zápis | Znamená |
| --- | --- |
| `%%~nxs` | jen jméno a přípona bez cesty |
| `%%~dps` | jen cesta bez jména |
| `%~1` | první argument bez uvozovek |

## Kde se to láme

| Chyba | Proč |
| --- | --- |
| `set A = 1` | kolem rovnítka nesmí být mezery |
| `set /a` chybí | do proměnné se uloží text `10+3` |
| `%i` v souboru | v dávkovém souboru musí být `%%i` |
| `if %1==x` a `%1` je prázdné | vždy dávej do uvozovek |
| `) else (` rozdělené na dva řádky | musí být na jednom |
| proměnná v cyklu se nemění | chybí `setlocal enabledelayedexpansion` a vykřičníky |
| `exit` místo `exit /b` | zavře celé okno |
| `if errorlevel 1` chytá i kód 5 | znamená "1 a vyšší" |
