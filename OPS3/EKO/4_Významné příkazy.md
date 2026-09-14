## Informace o systému

```
hostname                  jméno počítače
whoami                    přihlášený uživatel
whoami /groups            ve kterých skupinách jsi
winver                    okno s verzí Windows
ver                       verze v příkazové řádce
systeminfo                všechno najednou: verze, paměť, síť, aktualizace
msinfo32                  podrobný přehled v okně
```

V PowerShellu:

```powershell
Get-ComputerInfo
Get-ComputerInfo | Select-Object OsName, OsVersion, CsTotalPhysicalMemory
Get-CimInstance Win32_Processor | Select-Object Name, NumberOfCores
Get-CimInstance Win32_BIOS
```

`wmic` najdeš ve starých návodech. Microsoft ho vyřadil a v nových Windows 11 už není. Místo něj se používá `Get-CimInstance`.

## Síť

| cmd | PowerShell | Co zjistí |
| --- | --- | --- |
| `ipconfig` | `Get-NetIPAddress` | IP adresa, maska, brána |
| `ipconfig /all` | `Get-NetIPConfiguration` | i MAC adresa a DNS servery |
| `ping google.com` | `Test-Connection google.com` | je cíl dostupný |
| `tracert google.com` | `Test-NetConnection google.com -TraceRoute` | kudy vedou pakety |
| `nslookup google.com` | `Resolve-DnsName google.com` | IP adresa podle jména |
| `netstat -ano` | `Get-NetTCPConnection` | otevřená spojení a porty |
| `getmac` | `Get-NetAdapter` | MAC adresy síťových karet |

```
ipconfig /flushdns        vymaže mezipaměť DNS
ping -n 10 google.com     10 pokusů místo 4
ping -t google.com        pořád dokola, ukončíš Ctrl+C
```

Je otevřený konkrétní port?

```powershell
Test-NetConnection google.com -Port 443
```

### Postup, když nejde internet

1. `ipconfig` - mám IP adresu? Když začíná `169.254`, počítač nedostal adresu od routeru.
2. `ping` na bránu - dosáhnu na router?
3. `ping 8.8.8.8` - dosáhnu do internetu?
4. `ping google.com` - funguje překlad jmen?

Když funguje třetí krok a čtvrtý ne, je problém v DNS, ne v připojení.

## Procesy

```
tasklist                          běžící procesy
tasklist | findstr chrome         jen Chrome
taskkill /IM notepad.exe          ukončí podle jména
taskkill /PID 1234                ukončí podle čísla procesu
taskkill /IM notepad.exe /F       ukončí násilím
```

```powershell
Get-Process
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
Stop-Process -Name notepad
```

## Služby

Služba je program, který běží na pozadí bez okna. Tiskový spooler, Windows Update, antivir.

```
sc query                          běžící služby
sc query spooler                  stav jedné služby
net start                         seznam spuštěných služeb
services.msc                      správa služeb v okně
```

```powershell
Get-Service
Get-Service | Where-Object Status -eq "Running"
Get-Service spooler
```

Spouštění a zastavování služeb vyžaduje příkazovou řádku spuštěnou **jako správce**.

## Disky a místo

```powershell
Get-PSDrive -PSProvider FileSystem
```

Vypíše jednotky, obsazené a volné místo. V `cmd` je volné místo vidět na posledním řádku `dir`.

Tyto příkazy vyžadují **správce**:

```
chkdsk C:                 zkontroluje souborový systém
sfc /scannow              zkontroluje a opraví systémové soubory
diskpart                  správa oddílů, opatrně
```

## Programy

```
winget list                      nainstalované programy
winget search vlc                vyhledá program
winget upgrade                   co jde aktualizovat
```

`winget` je správce balíčků pro Windows. Funguje podobně jako `apt` v Linuxu.

## Vypnutí a restart

```
shutdown /s /t 0          vypne hned
shutdown /r /t 0          restartuje hned
shutdown /s /t 600        vypne za 10 minut
shutdown /a               zruší naplánované vypnutí
shutdown /l               odhlásí
```

**Ve škole si nevypínej počítač omylem.** Zkoušej `shutdown /s /t 600` a hned potom `shutdown /a`.

## Přesměrování výstupu

| Zápis | Co udělá |
| --- | --- |
| `> soubor.txt` | výstup do souboru, **přepíše** ho |
| `>> soubor.txt` | výstup na konec souboru |
| `2> chyby.txt` | chybové hlášky do souboru |
| `> vse.txt 2>&1` | výstup i chyby do jednoho souboru |
| `2> nul` | chyby zahodí |
| `< soubor.txt` | vstup ze souboru |

```
ipconfig /all > sit.txt
echo %date% %time% >> log.txt
dir C:\neexistuje 2> nul
```

**`>` soubor bez varování přepíše.** Když chceš přidávat, musí tam být `>>`.

Ve **Windows PowerShell 5.1** ukládá `>` soubory v kódování UTF-16. `findstr` v takovém souboru nic nenajde a některé programy ho zobrazí rozsypaný. Bezpečnější je:

```powershell
Get-Process | Out-File procesy.txt -Encoding utf8
```

## Roura v cmd

```
tasklist | findstr /i chrome
ipconfig /all | findstr /i "IPv4 DNS"
dir /s /b C:\Windows\*.log | find /c /v ""
systeminfo | findstr /c:"Total Physical Memory"
```

| Parametr `findstr` | Co udělá |
| --- | --- |
| `/i` | nezáleží na velikosti písmen |
| `/v` | řádky, které text NEobsahují |
| `/n` | i s čísly řádků |
| `/c:"text"` | hledá celý text i s mezerami |
| `"a b"` | hledá `a` nebo `b` |

`find /c /v ""` spočítá řádky. Je to trik, protože `cmd` nemá příkaz na počítání.

Výstupy `systeminfo` se liší podle jazyka systému. V české verzi hledej `Celková fyzická paměť`.

## Schránka

```
ipconfig | clip                  výstup do schránky
hostname | clip
```

```powershell
Get-Process | Out-String | Set-Clipboard
Get-Clipboard
```

Hodí se, když potřebuješ výstup poslat do mailu nebo do Teams.

## Spojování příkazů v cmd

| Zápis | Kdy se spustí druhý |
| --- | --- |
| `a & b` | vždycky |
| `a && b` | jen když `a` uspěl |
| `a \|\| b` | jen když `a` selhal |

```
md zaloha && cd zaloha
ping -n 1 192.168.1.1 > nul && echo Brana odpovida || echo Brana neodpovida
```

## Užitečné zkratky

```
start .                          otevře aktuální složku v Průzkumníku
notepad soubor.txt               otevře soubor v Poznámkovém bloku
start https://github.com         otevře stránku v prohlížeči
```

Soubor přetažený myší do okna konzole vloží svoji celou cestu.

```powershell
Get-Process | Out-GridView
```

Otevře výsledek jako tabulku v okně, kde jde řadit a filtrovat.
