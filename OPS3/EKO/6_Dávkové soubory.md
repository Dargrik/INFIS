## Proč automatizovat

Každou činnost, kterou jsi udělal dvakrát stejně, udělá počítač lépe než ty. Nezapomene krok, neudělá překlep a zvládne to v noci.

Typické případy ve správě Windows:

- zálohovat složku na síťový disk,
- smazat dočasné soubory na deseti počítačích,
- připravit nový počítač podle stejného postupu,
- spustit sadu programů jedním kliknutím,
- sebrat z počítače informace do souboru.

## Čím se to dělá

| Formát | Co to je | Kdy použít |
| --- | --- | --- |
| `.bat` | dávkový soubor, příkazy cmd | jednoduché věci, funguje všude |
| `.cmd` | totéž, novější přípona | stejné jako `.bat` |
| `.ps1` | skript PowerShellu | složitější úlohy, práce s objekty |

Mezi `.bat` a `.cmd` je prakticky jen jeden drobný rozdíl v chování proměnné `ERRORLEVEL`. Pro tebe jsou zaměnitelné. Starší návody používají `.bat`, novější `.cmd`.

Dávkový soubor je **obyčejný textový soubor**. Příkazy v něm jsou úplně stejné jako ty, které píšeš do okna cmd.

## První dávkový soubor

Otevři Poznámkový blok a napiš:

```
@echo off
echo Ahoj
echo Jsem v adresari:
cd
pause
```

Ulož jako `pokus.bat`. **V dialogu Uložit jako přepni "Typ souboru" na "Všechny soubory"**, jinak vznikne `pokus.bat.txt`.

Spustíš dvojklikem nebo z příkazové řádky:

```
pokus.bat
.\pokus.bat
```

### Co dělají ty řádky

| Řádek | Význam |
| --- | --- |
| `@echo off` | vypne vypisování samotných příkazů |
| `echo text` | vypíše text |
| `pause` | počká na stiskem klávesy |
| `rem text` | komentář, nic neudělá |
| `::text` | komentář, kratší zápis |

Zkus `@echo off` z první řádky smazat a soubor pustit znovu. Uvidíš každý příkaz dvakrát: jednou jak je napsaný a jednou jeho výstup. Proto se `@echo off` píše skoro vždycky.

Zavináč před příkazem potlačí vypsání jen toho jednoho řádku. Proto `@echo off`, a ne `echo off`.

## Proč okno zmizí

Když soubor spustíš dvojklikem, cmd se otevře, odpracuje to a zavře se dřív, než stihneš cokoli přečíst.

Řešení jsou dvě:

```
pause
```

na konci souboru, nebo soubor spouštět z už otevřeného okna cmd.

## Komentáře a hlavička

```
@echo off
rem zaloha.bat - zkopiruje dokumenty na disk D
rem Pouziti: zaloha.bat
rem Autor: Novak, 3.A, listopad 2026

echo Zalohuju...
```

**V dávkových souborech nepoužívej háčky a čárky.** Okno cmd má jiné kódování než Poznámkový blok a místo "Zálohuju" uvidíš "Z lohuju". Buď piš bez diakritiky, nebo soubor ulož v kódování ANSI.

## Cesty a aktuální adresář

Tohle je nejčastější důvod, proč dávkový soubor funguje u tebe a u kolegy ne.

```
@echo off
echo Soubor lezi v: %~dp0
echo Aktualni adresar je: %CD%
```

| Zápis | Znamená |
| --- | --- |
| `%~dp0` | složka, ve které leží sám dávkový soubor |
| `%CD%` | adresář, ve kterém běží okno |
| `%0` | jméno dávkového souboru |

Když soubor spustíš z Plánovače úloh nebo z jiné složky, `%CD%` je úplně jinde, než čekáš. Bezpečný začátek skriptu:

```
@echo off
cd /d "%~dp0"
```

`/d` umožní přepnout i na jiný disk. Bez něj `cd D:\data` z disku C jen změní aktuální složku na D, ale nepřepne tam.

## Výpis do souboru

```
@echo off
echo Report ze dne %DATE% > report.txt
ipconfig >> report.txt
echo. >> report.txt
systeminfo | findstr /C:"OS Name" >> report.txt
echo Hotovo, vysledek je v report.txt
```

| Zápis | Co udělá |
| --- | --- |
| `> soubor` | zapíše a přepíše |
| `>> soubor` | přidá na konec |
| `2> soubor` | uloží chybové hlášky |
| `> nul` | výstup zahodí |
| `echo.` | prázdný řádek |

**Jedna šipka soubor bez varování přepíše.** Když chceš sbírat, musí tam být dvě.

## Volání jiného programu

```
@echo off
notepad report.txt
echo Tohle se vypise az po zavreni Poznamkoveho bloku
```

Dávkový soubor na program počká. Když čekat nechceš:

```
start notepad report.txt
start "" "C:\Program Files\VideoLAN\VLC\vlc.exe"
```

**První uvozovky za `start` jsou název okna.** Když je vynecháš a cesta je v uvozovkách, cmd ji považuje za název okna a nic nespustí. Proto se tam píšou prázdné uvozovky.

Volání jiného dávkového souboru:

```
call priprava.bat
echo Pokracuju dal
```

Bez `call` by se běh přesunul do druhého souboru a už se nevrátil.

## Pauza bez klávesy

```
timeout /t 10
timeout /t 10 /nobreak
ping -n 11 127.0.0.1 > nul
```

`timeout` počká zadaný počet sekund. S `/nobreak` ho nejde přeskočit mezerníkem. Třetí varianta je starý trik pro systémy, kde `timeout` není.

## Návratový kód

Každý program po sobě nechá číslo v proměnné `ERRORLEVEL`. Nula znamená v pořádku.

```
@echo off
ping -n 1 seznam.cz > nul
echo Navratovy kod: %ERRORLEVEL%
```

Používá se k tomu, aby skript poznal, jestli krok vyšel. Podrobně to probereme v další lekci u podmínek.

## Praktický skript: záloha

```
@echo off
cd /d "%~dp0"

set ZDROJ=%USERPROFILE%\Documents
set CIL=D:\Zalohy\Dokumenty

echo Zalohuji %ZDROJ%
echo do %CIL%
echo.

robocopy "%ZDROJ%" "%CIL%" /E /R:2 /W:5 /LOG+:zaloha.log

echo.
echo Hotovo. Podrobnosti jsou v zaloha.log
pause
```

| Parametr robocopy | Co dělá |
| --- | --- |
| `/E` | i podsložky včetně prázdných |
| `/MIR` | zrcadlí, **maže v cíli navíc soubory** |
| `/R:2` | dva pokusy při chybě |
| `/W:5` | pět sekund mezi pokusy |
| `/LOG+:soubor` | zapisuje do logu a přidává |
| `/XD slozka` | tuhle složku vynechá |

**`/MIR` používej opatrně.** Když prohodíš zdroj a cíl, smaže ti to data. `/E` jen kopíruje a nic nemaže.

`robocopy` vrací kódy 0 až 7 jako úspěch, 8 a výš jako chybu. To je rozdíl proti ostatním programům.

## Praktický skript: úklid dočasných souborů

```
@echo off
echo Mazu docasne soubory...

del /q /s "%TEMP%\*" > nul 2>&1
del /q /s "C:\Windows\Temp\*" > nul 2>&1

echo Hotovo.
pause
```

| Parametr | Co dělá |
| --- | --- |
| `/q` | nezeptá se na potvrzení |
| `/s` | i v podsložkách |
| `/f` | smaže i soubory jen ke čtení |

Část `> nul 2>&1` zahodí výpis i hlášky o souborech, které zrovna někdo používá. Ty jsou u dočasných souborů normální.

## Spuštění jako správce

Některé příkazy potřebují zvýšená práva. Dávkový soubor spustíš pravým tlačítkem a **Spustit jako správce**.

Když má jít o správce vždycky, vytvoř zástupce, v jeho vlastnostech otevři **Upřesnit** a zaškrtni **Spustit jako správce**.

## Spuštění podle plánu

Dávkový soubor se dá navěsit na čas nebo na událost v **Plánovači úloh**:

```
taskschd.msc
```

Vytvoř úlohu, v záložce Spouštěče nastav kdy, v záložce Akce vyber svůj `.bat`. V nastavení úlohy nezapomeň vyplnit **Spustit v** na složku se skriptem, jinak poběží z `C:\Windows\System32`.

## Kde se to láme

| Problém | Co s tím |
| --- | --- |
| Vznikne `skript.bat.txt` | v Poznámkovém bloku přepni typ na Všechny soubory |
| Okno hned zmizí | chybí `pause` na konci |
| Diakritika se rozsype | piš bez háčků, nebo ulož v ANSI |
| Funguje ručně, z plánovače ne | chybí `cd /d "%~dp0"` nebo práva správce |
| `start "C:\...\program.exe"` nic nespustí | chybí prázdné uvozovky pro název okna |
| Po zavolání druhého `.bat` se skript ukončí | chybí `call` |
| Cesta s mezerou nefunguje | dej ji do uvozovek |
| `/MIR` smazal data | zkontroluj pořadí zdroj, cíl |
