## Proč PowerShell místo dávky

Dávkový soubor zvládne hodně, ale u složitější úlohy se brzy dostaneš k vykřičníkům, `goto` a hádání, proč se proměnná nemění. PowerShell je skutečný programovací jazyk a většinu těchto nástrah nemá.

| | dávkový soubor | PowerShell |
| --- | --- | --- |
| Pracuje s | textem | objekty |
| Proměnné v cyklu | potřebují vykřičníky | fungují normálně |
| Podmínky | `if exist`, `EQU` | `if`, `-eq`, `Test-Path` |
| Filtrování | `findstr` | `Where-Object` |
| Výstup | text do souboru | CSV, JSON, HTML |
| Chybové stavy | `ERRORLEVEL` | `try` a `catch` |

Dávku použij, když je úloha na pět řádků a má běžet všude. PowerShell, když se má něco rozhodovat, filtrovat nebo reportovat.

## První skript

Otevři **Windows PowerShell ISE** nebo Poznámkový blok a napiš:

```powershell
Write-Host "Ahoj"
Write-Host "Jsem v adresari:"
Get-Location
```

Ulož jako `pokus.ps1`.

## Spouštění skriptů

Při prvním pokusu dostaneš tohle:

```
pokus.ps1 : File ... cannot be loaded because running scripts
is disabled on this system.
```

To není chyba ve skriptu. Windows mají ve výchozím stavu spouštění skriptů vypnuté.

```powershell
Get-ExecutionPolicy
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

| Politika | Co dovolí |
| --- | --- |
| `Restricted` | nic, výchozí stav |
| `RemoteSigned` | vlastní skripty ano, stažené jen podepsané |
| `AllSigned` | jen podepsané |
| `Unrestricted` | všechno, nepoužívej |

`RemoteSigned` se `-Scope CurrentUser` je rozumná volba. Nepotřebuje práva správce a mění nastavení jen tobě.

Spuštění:

```powershell
.\pokus.ps1
C:\Skripty\pokus.ps1
```

**Tečka lomítko je povinné.** PowerShell záměrně nespouští programy z aktuální složky, aby ti podstrčený `ls.ps1` nespustil něco jiného, než čekáš.

## Komentáře a hlavička

```powershell
# zaloha.ps1 - zkopiruje dokumenty na disk D
# Pouziti: .\zaloha.ps1
# Autor: Novak, 3.A, listopad 2026

<#
    Delsi komentar
    pres vic radku
#>
```

Soubor `.ps1` ukládej v kódování **UTF-8 s BOM**, pak funguje i diakritika. V ISE je to výchozí, v Poznámkovém bloku vyber v dialogu Uložit jako kódování UTF-8.

## Proměnné

```powershell
$jmeno = "Novák"
$pocet = 5
$dnes = Get-Date

Write-Host "Ahoj $jmeno"
Write-Host "Souborů: $pocet"
```

Proměnná začíná dolarem při zápisu i při čtení. Kolem rovnítka mezery **být můžou**, na rozdíl od dávky.

```powershell
$pocet = 5
$pocet = $pocet + 1
$pocet++
$pocet += 10
```

Na rozdíl od dávky umí PowerShell desetinná čísla:

```powershell
10 / 3                 3,33333333333333
[math]::Round(10/3, 2) 3,33
```

### Uvozovky

```powershell
$jmeno = "Novák"
Write-Host "Ahoj $jmeno"      Ahoj Novák
Write-Host 'Ahoj $jmeno'      Ahoj $jmeno
```

Dvojité uvozovky proměnnou dosadí, jednoduché ne. Když chceš do textu výsledek výrazu:

```powershell
Write-Host "Souborů: $((Get-ChildItem).Count)"
```

### Typy

```powershell
$a = 5
$b = "5"
$a + 5        10
$b + 5        55
```

Textová pětka se k číslu připojí, nesečte. Když si nejsi jistý, zeptej se:

```powershell
$a.GetType().Name     Int32
$b.GetType().Name     String
[int]$b + 5           10
```

## Výstup

```powershell
Write-Host "Vidí jen člověk u obrazovky"
Write-Output "Pokračuje rourou dál"
Write-Warning "Varování"
Write-Error "Chyba"
```

`Write-Host` píše rovnou na obrazovku a nedá se přesměrovat do souboru. Ve skriptu, jehož výstup chceš dál zpracovávat, používej `Write-Output`.

```powershell
Write-Host "Hotovo" -ForegroundColor Green
Write-Host "Pozor" -ForegroundColor Yellow
```

## Vstup od uživatele

```powershell
$jmeno = Read-Host "Jak se jmenuješ"
Write-Host "Ahoj $jmeno"

$heslo = Read-Host "Heslo" -AsSecureString
```

## Parametry skriptu

Tohle je čistší náhrada za `%1` z dávkových souborů. Musí být na **prvním řádku** skriptu, před ním jen komentáře.

```powershell
param(
    [string]$Slozka,
    [int]$Dny = 30
)

Write-Host "Složka: $Slozka"
Write-Host "Starší než $Dny dní"
```

```powershell
.\uklid.ps1 -Slozka "C:\Logy" -Dny 7
.\uklid.ps1 "C:\Logy" 7
```

Parametr s výchozí hodnotou je nepovinný. Povinný se označí takhle:

```powershell
param(
    [Parameter(Mandatory=$true)][string]$Slozka
)
```

Když ho uživatel nezadá, PowerShell se na něj sám zeptá.

## Podmínky

```powershell
if ($pocet -gt 100) {
    Write-Host "Přes sto"
} elseif ($pocet -gt 10) {
    Write-Host "Přes deset"
} else {
    Write-Host "Málo"
}
```

| Operátor | Znamená |
| --- | --- |
| `-eq` | rovná se |
| `-ne` | nerovná se |
| `-gt`, `-ge` | větší, větší nebo rovno |
| `-lt`, `-le` | menší, menší nebo rovno |
| `-like` | odpovídá masce s `*` |
| `-match` | odpovídá regulárnímu výrazu |
| `-contains` | seznam obsahuje prvek |

**Nepiš `>` a `<`.** V PowerShellu znamenají přesměrování do souboru, ne porovnání. `if ($a > 5)` ti vytvoří soubor jménem 5.

```powershell
if ($jmeno -eq "Novák") { }
if ($soubor.Name -like "*.log") { }
if (-not $jmeno) { Write-Host "Prázdné" }
if ($a -gt 5 -and $b -lt 10) { }
if ($a -eq 1 -or $a -eq 2) { }
```

### Existuje soubor

```powershell
if (Test-Path "C:\Data") {
    Write-Host "Složka existuje"
} else {
    New-Item -ItemType Directory "C:\Data"
}
```

```powershell
if (-not (Test-Path $cil)) { New-Item -ItemType Directory $cil | Out-Null }
```

`Out-Null` zahodí výpis o vytvořené složce, který by jinak skončil ve výstupu.

### switch

```powershell
switch ($volba) {
    "start"   { Write-Host "Spouštím" }
    "stop"    { Write-Host "Zastavuji" }
    "restart" { Write-Host "Restartuji" }
    default   { Write-Host "Neznámá volba" }
}
```

## Cykly

### foreach

```powershell
foreach ($i in 1..5) {
    Write-Host "Kolo $i"
}

foreach ($soubor in Get-ChildItem *.txt) {
    Write-Host $soubor.Name
}
```

```powershell
$sluzby = Get-Service
foreach ($s in $sluzby) {
    if ($s.Status -eq "Running") {
        Write-Host $s.Name
    }
}
```

### ForEach-Object v rouře

```powershell
Get-ChildItem *.txt | ForEach-Object { Write-Host $_.Name }
Get-ChildItem *.log | ForEach-Object { $_.Name.ToUpper() }
```

`$_` je aktuální objekt, stejně jako u `Where-Object`.

### for a while

```powershell
for ($i = 1; $i -le 10; $i++) {
    Write-Host $i
}

$i = 1
while ($i -le 5) {
    Write-Host $i
    $i++
}

do {
    $odpoved = Read-Host "Pokračovat? (a/n)"
} while ($odpoved -ne "n")
```

| Příkaz | Co udělá |
| --- | --- |
| `break` | ukončí cyklus |
| `continue` | přeskočí na další kolo |

## Ošetření chyb

```powershell
try {
    Copy-Item "C:\Data\*" "D:\Zaloha" -ErrorAction Stop
    Write-Host "Záloha hotova" -ForegroundColor Green
}
catch {
    Write-Host "Záloha selhala: $($_.Exception.Message)" -ForegroundColor Red
    exit 1
}
finally {
    Write-Host "Konec"
}
```

**Bez `-ErrorAction Stop` se do `catch` nedostaneš.** Většina rutin chybu jen vypíše a pokračuje dál.

## Funkce

Když se část skriptu opakuje, vytáhni ji do funkce.

```powershell
function Zapis-Log {
    param([string]$Zprava)
    $cas = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    Add-Content -Path "zaloha.log" -Value "$cas  $Zprava"
}

Zapis-Log "Začínám"
Zapis-Log "Hotovo"
```

Funkce musí být ve skriptu **napsaná dřív**, než ji zavoláš. Jméno se podle zvyklostí skládá ze slovesa a podstatného jména, stejně jako u rutin.

## Výstup do souboru

```powershell
Get-Process | Export-Csv -Path "procesy.csv" -NoTypeInformation -Encoding UTF8
Get-Service | ConvertTo-Html | Out-File "sluzby.html"
Get-ChildItem | Out-File "seznam.txt"
"Řádek" | Add-Content "log.txt"
```

Tady je hlavní výhoda proti dávce. Z dávkového souboru dostaneš text, z PowerShellu rovnou tabulku, kterou otevřeš v Excelu.

## Celý příklad: úklid a report

```powershell
# uklid.ps1 - smaze stare logy a zapise report
# Pouziti: .\uklid.ps1 -Slozka "C:\Logy" -Dny 30

param(
    [Parameter(Mandatory=$true)][string]$Slozka,
    [int]$Dny = 30
)

function Zapis-Log {
    param([string]$Zprava)
    $cas = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    Write-Host "$cas  $Zprava"
    Add-Content -Path "uklid.log" -Value "$cas  $Zprava"
}

if (-not (Test-Path $Slozka)) {
    Write-Host "Složka $Slozka neexistuje" -ForegroundColor Red
    exit 1
}

$hranice = (Get-Date).AddDays(-$Dny)
$stare = Get-ChildItem -Path $Slozka -Filter *.log -Recurse |
         Where-Object { $_.LastWriteTime -lt $hranice }

Zapis-Log "Nalezeno souborů ke smazání: $($stare.Count)"

$usetreno = 0
foreach ($soubor in $stare) {
    try {
        $usetreno += $soubor.Length
        Remove-Item $soubor.FullName -ErrorAction Stop
        Zapis-Log "Smazáno: $($soubor.Name)"
    }
    catch {
        Zapis-Log "Nelze smazat: $($soubor.Name)"
    }
}

$mb = [math]::Round($usetreno / 1MB, 2)
Zapis-Log "Uvolněno $mb MB"
```

```powershell
.\uklid.ps1 -Slozka "C:\Windows\Temp" -Dny 7
```

**Než pustíš něco, co maže, nahraď `Remove-Item` za `Write-Host`** a podívej se, co by to smazalo. Nebo použij `-WhatIf`:

```powershell
Remove-Item $soubor.FullName -WhatIf
```

`-WhatIf` jen vypíše, co by se stalo, a neudělá nic. Umí ho většina rutin, které něco mění.

## Spuštění podle plánu

V Plánovači úloh se `.ps1` nespouští přímo. Jako program zadej:

```
powershell.exe
```

a jako argumenty:

```
-ExecutionPolicy Bypass -File "C:\Skripty\uklid.ps1" -Slozka "C:\Logy"
```

Přidej ještě `-NoProfile`, ať se úloha nezdrží načítáním tvého profilu.

## Kde se to láme

| Chyba | Proč |
| --- | --- |
| `running scripts is disabled` | je potřeba `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` |
| `pokus.ps1` nejde spustit | chybí `.\` |
| `if ($a > 5)` vytvoří soubor | porovnání je `-gt`, ne `>` |
| `"5" + 5` vrátí `55` | text se připojuje, přetypuj přes `[int]` |
| `catch` se nespustí | chybí `-ErrorAction Stop` |
| diakritika se rozsype | ulož soubor v UTF-8 s BOM |
| `param()` nefunguje | musí být na prvním řádku skriptu |
| funkce není nalezena | volání je dřív než její definice |
| skript z plánovače nic neudělá | chybí `-File` nebo plná cesta |
