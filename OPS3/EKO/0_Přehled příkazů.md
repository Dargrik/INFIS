# Přehled příkazů

## Orientace v cmd

| Příkaz | Ukázka |
| --- | --- |
| `cd` | `cd C:\Windows`<br>`cd ..`<br>`cd \`<br>`cd /d D:\data`<br>`cd %USERPROFILE%`<br>`cd` |
| `dir` | `dir`<br>`dir /a`<br>`dir /b`<br>`dir /s`<br>`dir /p`<br>`dir *.txt`<br>`dir /a:h` |
| `tree` | `tree`<br>`tree /f` |
| `cls` | `cls` |
| `exit` | `exit` |
| `ver` | `ver` |
| `winver` | `winver` |
| `where` | `where ping`<br>`where notepad` |
| `doskey` | `doskey /history` |
| `chcp` | `chcp 65001` |
| `help` | `help`<br>`help dir` |
| `/?` | `dir /?`<br>`robocopy /?` |

## Soubory a složky

| Příkaz | Ukázka |
| --- | --- |
| `md` | `md pokus`<br>`md a\b\c` |
| `rd` | `rd slozka`<br>`rd /s slozka` |
| `copy` | `copy soubor.txt kopie.txt`<br>`copy *.txt D:\zaloha` |
| `xcopy` | `xcopy /s /e slozka cil` |
| `robocopy` | `robocopy slozka cil /E`<br>`robocopy "%ZDROJ%" "%CIL%" /E /R:2 /W:5 /LOG+:zaloha.log`<br>`robocopy zdroj cil /MIR /XD temp` |
| `move` | `move soubor.txt D:\` |
| `ren` | `ren stary.txt novy.txt` |
| `del` | `del soubor.txt`<br>`del /s *.tmp`<br>`del /q /s "%TEMP%\*"` |
| `type` | `type soubor.txt`<br>`type nul > soubor.txt` |
| `more` | `more soubor.txt` |
| `attrib` | `attrib soubor.txt`<br>`attrib +h soubor.txt`<br>`attrib -h soubor.txt` |
| `notepad` | `notepad soubor.txt`<br>`notepad $PROFILE` |

## Hledání

| Příkaz | Ukázka |
| --- | --- |
| `dir /s /b` | `dir C:\Windows\*.log /s /b` |
| `findstr` | `systeminfo \| findstr /C:"OS Name"`<br>`ipconfig \| findstr /C:"IPv4"`<br>`findstr /i heslo soubor.txt` |
| `where` | `where ping` |

## Přesměrování a roura v cmd

| Zápis | Ukázka |
| --- | --- |
| `>` | `ipconfig > sit.txt` |
| `>>` | `ipconfig >> report.txt` |
| `2>` | `dir C:\neexistuje 2> chyby.txt` |
| `> nul` | `del /q "%TEMP%\*" > nul` |
| `2>&1` | `del /q /s "%TEMP%\*" > nul 2>&1` |
| `\|` | `tasklist \| findstr chrome` |
| `echo.` | `echo. >> report.txt` |
| `clip` | `ipconfig \| clip` |

## Spojování příkazů v cmd

| Zápis | Ukázka |
| --- | --- |
| `&` | `echo a & echo b` |
| `&&` | `md zaloha && copy *.txt zaloha` |
| `\|\|` | `ping -n 1 seznam.cz \|\| echo nefunguje` |
| `%ERRORLEVEL%` | `echo %ERRORLEVEL%` |

## Systém, procesy a síť

| Příkaz | Ukázka |
| --- | --- |
| `systeminfo` | `systeminfo`<br>`systeminfo \| findstr /C:"OS Name"` |
| `hostname` | `hostname` |
| `msinfo32` | `msinfo32` |
| `tasklist` | `tasklist`<br>`tasklist \| findstr chrome` |
| `taskkill` | `taskkill /IM notepad.exe`<br>`taskkill /PID 1234 /F`<br>`taskkill /IM explorer.exe /F & start explorer` |
| `sc` | `sc query spooler` |
| `net` | `net start`<br>`net user`<br>`net user %USERNAME%`<br>`net localgroup` |
| `ipconfig` | `ipconfig`<br>`ipconfig /all`<br>`ipconfig /flushdns` |
| `ping` | `ping seznam.cz`<br>`ping -n 1 seznam.cz`<br>`ping -t seznam.cz` |
| `shutdown` | `shutdown /s /t 0`<br>`shutdown /r /t 0`<br>`shutdown /l`<br>`shutdown /a` |
| `chkdsk` | `chkdsk C:` |
| `sfc` | `sfc /scannow` |
| `winget` | `winget list`<br>`winget search vlc`<br>`winget upgrade --all` |
| `whoami` | `whoami`<br>`whoami /groups` |
| `tzutil` | `tzutil /g`<br>`tzutil /l` |
| `powercfg` | `powercfg /list`<br>`powercfg /getactivescheme`<br>`powercfg /change monitor-timeout-ac 15` |

## Proměnné prostředí

| Příkaz | Ukázka |
| --- | --- |
| `set` | `set`<br>`set PATH`<br>`set PROJEKT=C:\Projekty`<br>`set "CESTA=C:\Program Files\App"` |
| `set /a` | `set /a VYSLEDEK=10+3`<br>`set /a POCET+=1` |
| `set /p` | `set /p JMENO=Jak se jmenujes?` |
| `setx` | `setx PROJEKT C:\Projekty` |
| `echo %VAR%` | `echo %USERPROFILE%`<br>`echo %PATH%`<br>`echo %DATE%` |
| `rundll32` | `rundll32 sysdm.cpl,EditEnvironmentVariables` |

Běžné proměnné: `%USERPROFILE%`, `%APPDATA%`, `%LOCALAPPDATA%`, `%PROGRAMDATA%`, `%PROGRAMFILES%`,
`%WINDIR%`, `%TEMP%`, `%USERNAME%`, `%COMPUTERNAME%`, `%CD%`, `%ERRORLEVEL%`.

## Nastavení a registr

| Příkaz | Ukázka |
| --- | --- |
| `control` | `control` |
| `.cpl` | `appwiz.cpl`<br>`ncpa.cpl`<br>`sysdm.cpl`<br>`mmsys.cpl`<br>`timedate.cpl`<br>`intl.cpl`<br>`powercfg.cpl`<br>`main.cpl` |
| `.msc` | `devmgmt.msc`<br>`services.msc`<br>`eventvwr.msc`<br>`diskmgmt.msc`<br>`taskschd.msc`<br>`compmgmt.msc` |
| `ms-settings:` | `start ms-settings:`<br>`start ms-settings:display`<br>`start ms-settings:windowsupdate` |
| `shell:` | `start shell:startup`<br>`start shell:sendto`<br>`start shell:recent` |
| `regedit` | `regedit` |
| `reg query` | `reg query "HKCU\Control Panel\International" /v sShortDate` |
| `reg add` | `reg add "HKCU\...\Advanced" /v HideFileExt /t REG_DWORD /d 0 /f` |
| `reg export` | `reg export "HKCU\...\Advanced" zaloha.reg` |

## Dávkové soubory

| Příkaz | Ukázka |
| --- | --- |
| `@echo off` | první řádek souboru |
| `echo` | `echo Ahoj`<br>`echo.` |
| `pause` | `pause` |
| `rem` | `rem komentar` |
| `::` | `:: komentar` |
| `cd /d "%~dp0"` | `cd /d "%~dp0"` |
| `start` | `start notepad`<br>`start "" "C:\Program Files\App\app.exe"`<br>`start https://github.com`<br>`start .` |
| `call` | `call priprava.bat` |
| `timeout` | `timeout /t 10`<br>`timeout /t 10 /nobreak` |
| `exit /b` | `exit /b 1` |
| `goto` | `goto :konec` |
| `setlocal` | `setlocal enabledelayedexpansion`<br>`endlocal` |

| Zápis | Ukázka |
| --- | --- |
| `%0` | `echo Skript: %0` |
| `%1` | `echo Prvni: %1` |
| `%*` | `echo Vsechny: %*` |
| `%~1` | `if not exist "%~1\" exit /b 2` |
| `%~dp0` | `cd /d "%~dp0"` |
| `%~nx1` | `set CIL=D:\Zalohy\%~nx1` |
| `%%~nxs` | `for %%s in (*.log) do echo %%~nxs` |
| `%%~zs` | `set /a CELKEM=!CELKEM!+%%~zs` |
| `!PROMENNA!` | `set /a POCET=!POCET!+1` |

## Podmínky v dávce

| Zápis | Ukázka |
| --- | --- |
| `if exist` | `if exist "report.txt" echo je` |
| `if not exist` | `if not exist "D:\Zalohy" mkdir "D:\Zalohy"` |
| `==` | `if "%1"=="start" echo Spoustim` |
| `/i` | `if /i "%1"=="START" echo Spoustim` |
| `if else` | `if exist a.txt (echo je) else (echo neni)` |
| `EQU` `NEQ` | `if %POCET% EQU 0 echo nic` |
| `GTR` `GEQ` | `if %POCET% GTR 100 echo hodne` |
| `LSS` `LEQ` | `if %POCET% LEQ 10 echo malo` |
| `%ERRORLEVEL%` | `if %ERRORLEVEL% EQU 0 echo OK` |

## Cykly v dávce

| Zápis | Ukázka |
| --- | --- |
| `for` | `for %%i in (jedna dve tri) do echo %%i`<br>`for %%s in (*.txt) do echo %%s` |
| `for /r` | `for /r %%s in (*.tmp) do del "%%s"` |
| `for /d` | `for /d %%a in (C:\Users\*) do echo %%a` |
| `for /l` | `for /l %%i in (1,1,10) do echo %%i` |
| `for /f` | `for /f "tokens=*" %%r in (seznam.txt) do echo %%r`<br>`for /f "skip=1 tokens=1,3 delims=;" %%a in (zaci.txt) do echo %%a %%b`<br>`for /f "tokens=2 delims=:" %%a in ('ipconfig ^\| findstr /C:"IPv4"') do echo %%a` |

V dávkovém souboru `%%i`, přímo v okně cmd `%i`.

## PowerShell, základ

| Příkaz | Ukázka |
| --- | --- |
| `Get-Help` | `Get-Help Get-ChildItem`<br>`Get-Help dir`<br>`Get-Help * -Noun Service`<br>`Update-Help` |
| `Get-Command` | `Get-Command -Verb Get`<br>`Get-Command -Noun Service`<br>`Get-Command *process*` |
| `Get-Alias` | `Get-Alias ls`<br>`Get-Alias cat` |
| `Get-History` | `Get-History` |
| `Get-Location` | `Get-Location` |
| `Set-Location` | `Set-Location D:\data` |
| `Get-ChildItem` | `Get-ChildItem`<br>`Get-ChildItem C:\Windows -Filter *.log -Recurse`<br>`Get-ChildItem env:`<br>`Get-ChildItem C:\Windows -Recurse \| Where-Object Length -gt 1MB` |
| `Get-Content` | `Get-Content soubor.txt`<br>`Get-Content soubor.txt -Head 5`<br>`Get-Content soubor.txt -Tail 5`<br>`Get-Content soubor.txt -Wait` |
| `New-Item` | `New-Item -ItemType Directory pokus`<br>`New-Item -ItemType File soubor.txt`<br>`New-Item -ItemType File -Path $PROFILE -Force` |
| `Remove-Item` | `Remove-Item soubor.txt`<br>`Remove-Item soubor.txt -WhatIf` |
| `Add-Content` | `"Radek" \| Add-Content log.txt` |
| `Test-Path` | `Test-Path $PROFILE`<br>`Test-Path "C:\Data"` |
| `Get-PSDrive` | `Get-PSDrive -PSProvider FileSystem` |

## PowerShell, objekty a roura

| Příkaz | Ukázka |
| --- | --- |
| `Select-Object` | `Get-Process \| Select-Object *`<br>`Get-Process \| Select-Object Name, CPU`<br>`Get-Process \| Select-Object -First 10` |
| `Get-Member` | `Get-Process \| Get-Member`<br>`Get-Process \| Get-Member -MemberType Property` |
| `Where-Object` | `Get-Process \| Where-Object { $_.CPU -gt 10 }`<br>`Get-ChildItem \| Where-Object Length -gt 1MB` |
| `Sort-Object` | `Get-Process \| Sort-Object CPU -Descending` |
| `Measure-Object` | `Get-Process \| Measure-Object`<br>`Get-ChildItem \| Measure-Object Length -Sum` |
| `ForEach-Object` | `Get-ChildItem *.txt \| ForEach-Object { $_.Name }` |
| `$_` | `Where-Object { $_.Status -eq "Running" }` |
| `Export-Csv` | `Get-Process \| Export-Csv procesy.csv -NoTypeInformation -Encoding UTF8` |
| `ConvertTo-Html` | `Get-Service \| ConvertTo-Html \| Out-File sluzby.html` |
| `Out-File` | `Get-ChildItem \| Out-File seznam.txt` |
| `Out-Null` | `New-Item -ItemType Directory "C:\Data" \| Out-Null` |

## PowerShell, systém a nastavení

| Příkaz | Ukázka |
| --- | --- |
| `Get-Process` | `Get-Process`<br>`Get-Process notepad` |
| `Stop-Process` | `Stop-Process -Name notepad` |
| `Get-Service` | `Get-Service`<br>`Get-Service spooler` |
| `Get-ComputerInfo` | `Get-ComputerInfo` |
| `Get-CimInstance` | `Get-CimInstance Win32_BIOS`<br>`Get-CimInstance Win32_Processor` |
| `Test-NetConnection` | `Test-NetConnection google.com -Port 443` |
| `Get-Clipboard` | `Get-Clipboard`<br>`"text" \| Set-Clipboard` |
| `Get-LocalUser` | `Get-LocalUser` |
| `Get-LocalGroup` | `Get-LocalGroup`<br>`Get-LocalGroupMember -SID S-1-5-32-544` |
| `Get-ItemProperty` | `Get-ItemProperty "HKCU:\Control Panel\International"` |
| `Set-ItemProperty` | `Set-ItemProperty "HKCU:\...\Advanced" -Name HideFileExt -Value 0` |
| `Get-TimeZone` | `Get-TimeZone` |
| `Get-Culture` | `Get-Culture` |
| `Set-Alias` | `Set-Alias np notepad` |
| `$env:` | `$env:USERPROFILE`<br>`$env:PROJEKT = "C:\Projekty"`<br>`$env:Path -split ";"` |
| `[Environment]` | `[Environment]::SetEnvironmentVariable("PROJEKT", "C:\Projekty", "User")` |

## PowerShell, skripty

| Příkaz | Ukázka |
| --- | --- |
| `Get-ExecutionPolicy` | `Get-ExecutionPolicy`<br>`Get-ExecutionPolicy -List` |
| `Set-ExecutionPolicy` | `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` |
| `.\` | `.\skript.ps1` |
| `$PROFILE` | `$PROFILE`<br>`notepad $PROFILE` |
| `Write-Host` | `Write-Host "Ahoj"`<br>`Write-Host "Hotovo" -ForegroundColor Green` |
| `Write-Output` | `Write-Output "Pokracuje rourou"` |
| `Write-Warning` | `Write-Warning "Pozor"` |
| `Write-Error` | `Write-Error "Chyba"` |
| `Read-Host` | `$jmeno = Read-Host "Jak se jmenujes"`<br>`Read-Host "Heslo" -AsSecureString` |
| `Get-Date` | `Get-Date`<br>`Get-Date -Format "yyyy-MM-dd"`<br>`(Get-Date).AddDays(-30)` |
| `param` | `param([string]$Slozka, [int]$Dny = 30)`<br>`param([Parameter(Mandatory=$true)][string]$Slozka)` |
| `function` | `function Zapis-Log { param([string]$Zprava) }` |
| `try catch` | `try { Copy-Item a b -ErrorAction Stop } catch { Write-Host $_.Exception.Message }` |
| `-WhatIf` | `Remove-Item soubor.txt -WhatIf` |
| `[math]::Round` | `[math]::Round(10/3, 2)` |
| `[int]` | `[int]$b + 5` |

## PowerShell, podmínky a cykly

| Zápis | Ukázka |
| --- | --- |
| `if` | `if ($pocet -gt 100) { Write-Host "hodne" }` |
| `elseif else` | `if ($a -eq 1) { } elseif ($a -eq 2) { } else { }` |
| `switch` | `switch ($volba) { "start" { } default { } }` |
| `-eq` `-ne` | `if ($a -eq 5) { }` |
| `-gt` `-ge` `-lt` `-le` | `if ($pocet -gt 100) { }` |
| `-like` | `if ($s.Name -like "*.log") { }` |
| `-and` `-or` `-not` | `if ($a -gt 1 -and $b -lt 10) { }` |
| `foreach` | `foreach ($i in 1..5) { Write-Host $i }`<br>`foreach ($s in Get-ChildItem *.txt) { $s.Name }` |
| `for` | `for ($i = 1; $i -le 10; $i++) { Write-Host $i }` |
| `while` | `while ($i -le 5) { Write-Host $i; $i++ }` |
| `do while` | `do { $a = Read-Host } while ($a -ne "n")` |
| `break` `continue` | `break` |

## Zástupné znaky a zkratky

| Zápis | Ukázka |
| --- | --- |
| `*` | `dir *.txt` |
| `?` | `dir soubor?.txt` |
| `"` | `cd "C:\Program Files"` |
| <kbd>Tab</kbd> | doplnění |
| <kbd>Ctrl</kbd>+<kbd>C</kbd> | přerušení |
| <kbd>F7</kbd> | historie v okně |
| <kbd>šipka nahoru</kbd> | předchozí příkaz |
