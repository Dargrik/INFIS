## Nastavení otevřené příkazem

Každé okno nastavení se dá otevřít příkazem. Je to rychlejší než klikat a funguje to stejně ve všech jazycích Windows.

### Ovládací panely

```
control                     Ovládací panely
appwiz.cpl                  Programy a funkce
ncpa.cpl                    Síťová připojení
sysdm.cpl                   Vlastnosti systému
mmsys.cpl                   Zvuk
timedate.cpl                Datum a čas
intl.cpl                    Oblast a formáty
powercfg.cpl                Možnosti napájení
main.cpl                    Myš
```

### Konzole pro správu

```
devmgmt.msc                 Správce zařízení
services.msc                Služby
eventvwr.msc                Prohlížeč událostí
diskmgmt.msc                Správa disků
taskschd.msc                Plánovač úloh
compmgmt.msc                Správa počítače
```

Většina z nich se otevře i bez správce, ale změny v nich provést nepůjdou.

### Aplikace Nastavení

```
start ms-settings:                    úvodní stránka
start ms-settings:display             obrazovka
start ms-settings:network             síť
start ms-settings:windowsupdate       aktualizace
start ms-settings:defaultapps         výchozí aplikace
start ms-settings:personalization     přizpůsobení
start ms-settings:about               informace o systému
```

### Speciální složky

```
start shell:startup          programy spouštěné po přihlášení
start shell:sendto           nabídka Odeslat
start shell:downloads        Stažené soubory
start shell:recent           naposledy otevřené soubory
```

Zástupce vložený do `shell:startup` se spustí při každém přihlášení.

## Proměnné prostředí

### Jen pro tohle okno

```
set                          vypíše všechny
set PROJEKT=C:\Projekty      nastaví
echo %PROJEKT%               použije
cd %PROJEKT%
```

```powershell
Get-ChildItem env:
$env:PROJEKT = "C:\Projekty"
$env:PROJEKT
```

Po zavření okna proměnná zmizí.

### Natrvalo

```
setx PROJEKT C:\Projekty
```

```powershell
[Environment]::SetEnvironmentVariable("PROJEKT", "C:\Projekty", "User")
```

**Změna se projeví až v nově otevřeném okně.** V tom, kde jsi `setx` spustil, proměnná pořád není.

| Úroveň | Platí pro | Potřebuje správce |
| --- | --- | --- |
| `User` | přihlášeného uživatele | ne |
| `Machine` | všechny uživatele | ano |

### PATH

`PATH` je seznam složek, ve kterých Windows hledají programy. Když napíšeš `notepad`, prochází je jednu po druhé.

```
echo %PATH%
where notepad
```

```powershell
$env:Path -split ";"
```

**Nikdy neměň PATH příkazem `setx PATH "%PATH%;..."`.** Spojí systémovou a uživatelskou část do jedné, ořízne ji na 1024 znaků a zbytek bez varování zahodí. Místo toho otevři editor proměnných:

```
rundll32 sysdm.cpl,EditEnvironmentVariables
```

Otevře se okno s proměnnými tvého účtu, kde upravíš `Path` po řádcích a nic nezkazíš.

## Uživatelský účet

```
whoami                      kdo jsem
whoami /groups              ve kterých skupinách
net user                    uživatelé na tomhle počítači
net user %USERNAME%         podrobnosti o mém účtu
net localgroup              skupiny na tomhle počítači
```

```powershell
Get-LocalUser
Get-LocalGroup
Get-LocalGroupMember -SID S-1-5-32-544
```

Poslední příkaz vypíše správce počítače. `S-1-5-32-544` je pevné číslo skupiny Administrators, které je stejné ve všech jazycích Windows.

Na počítači v doméně, třeba ve škole, jsou účty uložené na serveru. `net user` pak ukáže jen místní účty a tvůj školní účet se zjistí přes `net user %USERNAME% /domain`.

## Registr z příkazové řádky

Většina nastavení, která měníš v okně, se zapíše do registru. Když víš kam, nastavíš je jedním příkazem.

| Zkratka | Větev |
| --- | --- |
| `HKCU` | `HKEY_CURRENT_USER`, tvoje nastavení |
| `HKLM` | `HKEY_LOCAL_MACHINE`, nastavení počítače, potřebuje správce |

### Čtení

```
reg query "HKCU\Control Panel\International" /v sShortDate
reg query "HKCU\Control Panel\Desktop" /v WallPaper
reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced"
```

```powershell
Get-ItemProperty "HKCU:\Control Panel\International" | Select-Object sShortDate, sDecimal
```

### Záloha před změnou

```
reg export "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" zaloha.reg
```

**Než cokoli v registru změníš, vyexportuj si klíč.** Obnovíš ho dvojklikem na `.reg` soubor nebo příkazem `reg import zaloha.reg`.

### Zápis

```
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v HideFileExt /t REG_DWORD /d 0 /f
```

| Část | Znamená |
| --- | --- |
| `/v HideFileExt` | název hodnoty |
| `/t REG_DWORD` | typ, tady číslo |
| `/d 0` | data, tedy nová hodnota |
| `/f` | bez ptaní |

Průzkumník si změnu přečte až po restartu:

```
taskkill /IM explorer.exe /F & start explorer
```

Obrazovka na chvíli zčerná a hlavní panel zmizí. To je v pořádku.

```powershell
Set-ItemProperty "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" -Name HideFileExt -Value 0
```

### Užitečná nastavení v HKCU

Všechna jsou v `HKCU\Software\Microsoft\Windows\CurrentVersion\`.

| Klíč | Hodnota | Co udělá |
| --- | --- | --- |
| `Explorer\Advanced` | `HideFileExt` = `0` | zobrazí přípony souborů |
| `Explorer\Advanced` | `Hidden` = `1` | zobrazí skryté soubory |
| `Explorer\Advanced` | `LaunchTo` = `1` | Průzkumník se otevře v Tento počítač |
| `Themes\Personalize` | `AppsUseLightTheme` = `0` | tmavý režim aplikací |
| `Themes\Personalize` | `SystemUsesLightTheme` = `0` | tmavý hlavní panel |

## Napájení

```
powercfg /list               plány napájení
powercfg /getactivescheme    aktivní plán
powercfg /change monitor-timeout-ac 15
powercfg /change standby-timeout-ac 0
```

`monitor-timeout-ac` je doba do vypnutí obrazovky v minutách při napájení ze sítě. `-dc` je totéž na baterii. `0` znamená nikdy.

Na některých počítačích, třeba ve škole, může změna plánu vyžadovat správce.

## Čas a jazyk

```powershell
Get-TimeZone
Get-Culture
Get-WinUserLanguageList
```

```
tzutil /g                    aktuální časové pásmo
tzutil /l                    všechna pásma
```

## Profil PowerShellu

Profil je skript, který PowerShell spustí při každém otevření. Je to místo pro vlastní aliasy a funkce.

```powershell
$PROFILE
Test-Path $PROFILE
New-Item -ItemType File -Path $PROFILE -Force
notepad $PROFILE
```

Do souboru napiš třeba:

```powershell
Set-Alias np notepad
function sit { ipconfig /all | Select-String "IPv4", "DNS" }
function kde { Get-Location; Get-ChildItem }
```

Ulož, zavři PowerShell a otevři ho znovu. Když se objeví chyba o spouštění skriptů, vrať se k části **Spouštění skriptů** z druhé lekce.

`New-Item ... -Force` existující profil přepíše. Použij ho, jen když `Test-Path` vrátil `False`.