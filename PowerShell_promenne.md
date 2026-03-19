---
- Proměnné začínají znakem `$` (např. `$name`)
- PowerShell je **object-based** – proměnná často drží **objekt**, ne jen text.

---

## 2) Vytvoření / nastavení proměnné

### Nejčastěji - přiřazení
```powershell
$name = "Karel"
$age  = 20
$pi   = 3.14159
```

### Pomocí cmdletů
```powershell
Set-Variable -Name "City" -Value "Plzeň"
New-Variable -Name "IsAdmin" -Value $false
```

### Vymazání / zrušení
```powershell
Clear-Variable -Name "name"      # nechá proměnnou existovat, jen vyprázdní hodnotu
Remove-Variable -Name "name"     # proměnnou odstraní
```

---

## 3) Vypsání proměnné (output)

```powershell
$name
Write-Output $name
Write-Output "Name: $name"
Write-Output ("Age: " + $age)
```

---

## 4) String interpolation

### Dvojité uvozovky interpolují proměnné
```powershell
$name = "Tobi"
Write-Output "Ahoj, $name!"

Výstup -> Ahoj, Tobi!
```

### Jednoduché uvozovky NEinterpolují
```powershell
Write-Output 'Ahoj, $name!'

Výstup -> Ahoj, $name!
```

### Subexpression `$(...)`
```powershell
$a = 10
$b = 32
Write-Output "Součet je: $($a + $b)"
```

### Formátovací operátor `-f`
```powershell
$price = 123.456

"Cena: {0} Kč" -f $price
# Cena: 123.456 Kč

"Cena: {0:N2} Kč" -f $price
# Cena: 123,46 Kč   (v CZ) / Cena: 123.46 Kč (např. v EN)

"Cena: {0:N0} Kč" -f $price
# Cena: 123 Kč

"Produkt: {0}, Cena: {1:N2} Kč" -f "Čaj", 123.456
# Produkt: Čaj, Cena: 123,46 Kč

N2 = number + obvykle tisícové oddělovače
# 12 345,60  (nebo 12,345.60)

F2 = fixed-point (bez tisícových oddělovačů)
# 12345,60   (nebo 12345.60)
```

---

## 5) Ověření, že proměnná existuje

### Varianta 1: `Test-Path variable:`
```powershell
Test-Path -Path "variable:name"      # True/False
```

### Varianta 2: `Get-Variable` + potlačení chyby
```powershell
$var = Get-Variable -Name "name" -ErrorAction SilentlyContinue
if ($null -ne $var) { "existuje" } else { "neexistuje" }
```

### Ověření, že má hodnotu (není `$null` nebo prázdná)
```powershell
$name = $null

if ($null -eq $name) { "je null" }
```

---

## 6) Základní operátory a práce s proměnnými

### Aritmetika
```powershell
$x = 5
$y = 2

$sum  = $x + $y
$diff = $x - $y
$mul  = $x * $y
$div  = $x / $y
$mod  = $x % $y
```

### Zkrácené přiřazení
```powershell
$count = 0
$count += 1
$count -= 2
$count *= 3
```

### Porovnávání (výsledkem je True/False)
```powershell
$a = 10
$b = 20

$a -eq $b   # rovná se
$a -ne $b   # nerovná se
$a -lt $b   # menší
$a -le $b   # menší nebo rovno
$a -gt $b   # větší
$a -ge $b   # větší nebo rovno
```

### Logické operátory
```powershell
$ok = ($a -lt $b) -and ($b -eq 20)
$ok2 = ($a -eq 10) -or ($b -eq 10)
$neg = -not $ok
```

### String operátory
```powershell
$text = "PowerShell"

$text -like "Power*"         # wildcard
$text -match "^Power"        # regex
$text -replace "Shell","BI"  # regex replace
```

---

## 7) Spojování a rozpojování (join / split)

### Spojování textů
```powershell
$first = "Vitek"
$last  = "Action"

$full1 = $first + " " + $last
$full2 = "$first $last"
```

### Spojování pole do textu: `-join`
```powershell
$parts = @("A", "B", "C")
$joined = $parts -join ", "
# "A, B, C"
```

### Rozdělení textu: `-split`
```powershell
$csvLine = "a;b;c"
$arr = $csvLine -split ";"
# @("a","b","c")
```

### Tip: split s limitem
```powershell
$text = "a=b=c"
$two = $text -split "=", 2
# @("a","b=c")
```

---

## 8) Typy proměnných

```powershell
$n = 10        # Int32
$s = "10"      # String
```

Když chceš typ „vynutit“:
```powershell
[int]$n = "10"
[double]$d = "3.14"
[datetime]$dt = "2026-02-27"
```

Zjištění typu:
```powershell
$n.GetType().FullName
```
---

## 9) If / Else

### Základní if / elseif / else
```powershell
$score = 72

if ($score -ge 90) {
  "A"
}
elseif ($score -ge 70) {
  "B"
}
else {
  "C"
}
```

### Častá chyba: porovnání vs přiřazení
- porovnání: `-eq`
- přiřazení: `=`

```powershell
$x = 5
if ($x -eq 5) { "ok" }
```

---

## 10) Switch

```powershell
$role = "admin"

switch ($role) {
  "admin" { "Plný přístup" }
  "user"  { "Běžný přístup" }
  default { "Neznámá role" }
}
```
---

## 11) Užitečné „speciální“ proměnné

```powershell
$null           # „nic“
$true / $false  # booleany
$_              # aktuální objekt v pipeline / foreach-object
$LASTEXITCODE   # exit code posledního externího programu
$Error          # kolekce chyb
```

Příklad s pipeline:
```powershell
Get-Process | ForEach-Object { $_.ProcessName }
```

---

## 12) Null-coalescing (PowerShell 7+)

```powershell
$name = $null
$display = $name ?? "Neznámý"
```

Nastav pouze pokud je null:
```powershell
$name ??= "Default"
```

---

# 13) Regex v PowerShellu

Regex = **regular expression** = pravidla pro hledání vzorů v textu.

Nepopisuje konkrétní text, ale **jak má text vypadat**.

Např.:
- hledat číslo
- hledat e-mail
- ověřit, že text začíná velkým písmenem
- najít všechna slova končící na `ing`
- nahradit určitý vzor jiným textem

V PowerShellu se regex používá hlavně s:
- `-match`
- `-notmatch`
- `-replace`
- `Select-String`

---

## 13.1) Nejjednodušší ukázka

```powershell
"ahoj Vitku" -match "Vitku"
# True
```

To znamená:
- v textu `"ahoj Vitku"`
- se hledá vzor `"Vitku"`
- protože tam je, výsledek je `True`

```powershell
"ahoj Vitku" -match "Karel"
# False
```

Tady se hledá `Karel`, ale v textu není.

---

## 13.2) `-match` v PowerShellu

Operátor `-match`:
- vrací `True` nebo `False`
- kontroluje, jestli text odpovídá regexu

```powershell
$text = "abc123"

$text -match "\d"
# True
```

Proč?
- `\d` znamená „nějaká číslice“
- v `abc123` číslice jsou

```powershell
"abc" -match "\d"
# False
```

Protože v `abc` žádná číslice není.

---

## 13.3) Nejdůležitější základní znaky regexu

### Tečka `.`
Tečka znamená:
- **libovolný jeden znak**

```powershell
"cat" -match "c.t"
# True
```

Protože:
- `c` = znak `c`
- `.` = libovolný jeden znak (`a`)
- `t` = znak `t`

Sedí tedy `cat`.

```powershell
"cot" -match "c.t"
# True

"cut" -match "c.t"
# True
```

Ale:

```powershell
"ct" -match "c.t"
# False
```

Protože tečka musí nahradit **právě jeden znak**.

### Zpětné lomítko `\`
Používá se pro:
- speciální zkratky
- escapování speciálních znaků

Např.:
- `\d` = číslice
- `\w` = znak slova
- `\s` = mezera
- `\.` = skutečná tečka

Příklad:

```powershell
"a.b" -match "\."
# True
```

Kdybys napsal jen `.`:

```powershell
"a.b" -match "."
# True
```

To by ale znamenalo „libovolný znak“, ne skutečnou tečku.

---

## 13.4) Základní „písmenka“ a zkratky

### `\d`
Znamená:
- **digit**
- jedna číslice `0–9`

```powershell
"7" -match "\d"
# True

"abc123" -match "\d"
# True

"abc" -match "\d"
# False
```

### `\D`
Opak `\d`:
- znak, který **není číslice**

```powershell
"a" -match "\D"
# True

"7" -match "\D"
# False
```

### `\w`
Znak „slova“:
- písmeno
- číslice
- podtržítko `_`

Prakticky:
- `[A-Za-z0-9_]`

```powershell
"A" -match "\w"
# True

"8" -match "\w"
# True

"_" -match "\w"
# True
```

### `\W`
Opak `\w`:
- znak, který není znak slova

```powershell
"@" -match "\W"
# True

" " -match "\W"
# True
```

### `\s`
Whitespace:
- mezera
- tab
- nový řádek

```powershell
"ahoj svet" -match "\s"
# True
```

Protože mezi slovy je mezera.

### `\S`
Opak `\s`:
- znak, který není whitespace

```powershell
" " -match "\S"
# False

"A" -match "\S"
# True
```

---

## 13.5) Doslovné znaky

Normální písmena a čísla znamenají sama sebe.

```powershell
"pes" -match "pes"
# True
```

```powershell
"kočka" -match "ko"
# True
```

Regex se nemusí shodovat s celým textem — často stačí, že najde **část**.

```powershell
"PowerShell" -match "Shell"
# True
```

---

## 13.6) Kotvy: začátek a konec textu

### `^`
Začátek řetězce

```powershell
"PowerShell" -match "^Power"
# True
```

```powershell
"SuperPower" -match "^Power"
# False
```

Protože `Power` není na začátku.

### `$`
Konec řetězce

```powershell
"PowerShell" -match "Shell$"
# True
```

```powershell
"ShellPower" -match "Shell$"
# False
```

### `^...$`
Celý text musí přesně odpovídat

```powershell
"123" -match "^\d+$"
# True
```

To znamená:
- `^` začátek
- `\d+` jedna nebo více číslic
- `$` konec

Tedy celý text musí být tvořen jen číslicemi.

```powershell
"123abc" -match "^\d+$"
# False
```

Protože tam nejsou jen číslice.

---

## 13.7) Četnosti / opakování

To je přesně to, co myslíš „četnosti“.

Regex umí říct:
- kolikrát se má něco opakovat

### `*`
0 nebo více opakování

```powershell
"ac" -match "ab*c"
# True
```

Protože:
- `a`
- žádné `b`
- `c`

```powershell
"abc" -match "ab*c"
# True

"abbbc" -match "ab*c"
# True
```

### `+`
1 nebo více opakování

```powershell
"ac" -match "ab+c"
# False
```

Protože tam musí být alespoň jedno `b`.

```powershell
"abc" -match "ab+c"
# True

"abbbbbc" -match "ab+c"
# True
```

### `?`
0 nebo 1 výskyt

```powershell
"color" -match "colou?r"
# True

"colour" -match "colou?r"
# True
```

Protože `u` je volitelné.

### `{n}`
Přesně `n` opakování

```powershell
"1234" -match "^\d{4}$"
# True
```

Přesně 4 číslice.

```powershell
"123" -match "^\d{4}$"
# False
```

### `{n,}`
Aspoň `n` opakování

```powershell
"12345" -match "^\d{4,}$"
# True
```

Aspoň 4 číslice.

### `{n,m}`
Od `n` do `m` opakování

```powershell
"123" -match "^\d{2,4}$"
# True

"12345" -match "^\d{2,4}$"
# False
```

---

## 13.8) Skupiny znaků pomocí hranatých závorek `[]`

### `[abc]`
Jeden znak z možností `a`, `b`, `c`

```powershell
"apple" -match "[abc]"
# True
```

Protože obsahuje `a`.

```powershell
"dog" -match "[abc]"
# False
```

### `[A-Z]`
Jedno velké písmeno

```powershell
"Hello" -match "[A-Z]"
# True
```

### `[a-z]`
Jedno malé písmeno

```powershell
"HELLO" -match "[a-z]"
# False
```

### `[0-9]`
Jedna číslice

To je podobné jako `\d`.

```powershell
"7" -match "[0-9]"
# True
```

### Kombinace v závorkách

```powershell
"abc123" -match "[a-z0-9]"
# True
```

### Negace `[^...]`
Znak, který **není** z dané množiny

```powershell
"7" -match "[^a-z]"
# True
```

Protože `7` není malé písmeno.

```powershell
"a" -match "[^a-z]"
# False
```

---

## 13.9) Skupiny pomocí kulatých závorek `()`

Používají se pro:
- seskupení části regexu
- aplikaci četnosti na víc znaků najednou
- zachytávání podvýrazů

Příklad:

```powershell
"haha" -match "(ha)+"
# True
```

To znamená:
- skupina `ha`
- opakovaná jednou nebo vícekrát

Sedí na:
- `ha`
- `hahaha`
- `hahahaha`

---

## 13.10) Alternativa pomocí `|`

Znamená:
- nebo

```powershell
"cat" -match "cat|dog"
# True

"dog" -match "cat|dog"
# True

"bird" -match "cat|dog"
# False
```

Seskupení:

```powershell
"grey" -match "gr(a|e)y"
# True

"gray" -match "gr(a|e)y"
# True
```

---

## 13.11) Praktické kombinace

### Jen čísla
```powershell
"12345" -match "^\d+$"
# True
```

### Přesně 3 číslice
```powershell
"123" -match "^\d{3}$"
# True
```

### Slovo začínající velkým písmenem
```powershell
"Tobi" -match "^[A-Z][a-z]+$"
# True
```

Vysvětlení:
- `^[A-Z]` = první znak velké písmeno
- `[a-z]+` = pak jedno nebo více malých písmen
- `$` = konec

### Jednoduchý formát PSČ
```powershell
"30100" -match "^\d{5}$"
# True
```

### Telefon ve formátu 123 456 789
```powershell
"123 456 789" -match "^\d{3}\s\d{3}\s\d{3}$"
# True
```

### Jednoduchý e-mail
```powershell
"test@example.com" -match "^\w+@\w+\.\w+$"
# True
```

Tohle je jen jednoduchá ukázka, ne úplně dokonalá validace všech e-mailů.

---

## 13.12) Co regex hledá: část vs celý text

Tohle je hodně důležité.

```powershell
"abc123xyz" -match "\d+"
# True
```

To neznamená, že celý text je číslo.
Jen to znamená, že **někde uvnitř** je jedna nebo více číslic.

Když chceš celý text:

```powershell
"abc123xyz" -match "^\d+$"
# False
```

---

## 13.13) Zachycené skupiny v PowerShellu: `$matches`

Když použiješ `-match`, PowerShell umí uložit nalezené části do proměnné `$matches`.

```powershell
$text = "Jmeno: Tobi"
$text -match "Jmeno:\s(\w+)"
$matches
```

Výsledek bude obsahovat:
- celý nalezený text
- zachycené skupiny

Např.:
- `$matches[0]` = celý match
- `$matches[1]` = první skupina

```powershell
$text = "Jmeno: Tobi"
$text -match "Jmeno:\s(\w+)"

$matches[0]
# Jmeno: Tobi

$matches[1]
# Tobi
```

---

## 13.14) Regex replace v PowerShellu

Pomocí `-replace` můžeš nahrazovat vzory.

```powershell
"abc123" -replace "\d", "#"
# abc###
```

Každá číslice se nahradí `#`.

```powershell
"2026-03-13" -replace "-", "."
# 2026.03.13
```

### Replace se skupinami

```powershell
"Novak Jan" -replace "(\w+)\s(\w+)", "$2 $1"
# Jan Novak
```

Vysvětlení:
- `(\w+)` = první slovo
- `\s` = mezera
- `(\w+)` = druhé slovo
- `$2 $1` = prohození pořadí

---

## 13.15) `Select-String`

Na hledání regexu v textu nebo souborech.

```powershell
"error 404" | Select-String "\d+"
```

Nebo v souboru:

```powershell
Select-String -Path ".\log.txt" -Pattern "ERROR"
```

---

## 13.16) Nejčastější speciální znaky regexu

```text
.       libovolný jeden znak
\d      číslice
\D      ne-číslice
\w      znak slova
\W      ne-znak slova
\s      whitespace
\S      ne-whitespace
^       začátek textu
$       konec textu
*       0 nebo více
+       1 nebo více
?       0 nebo 1
{n}     přesně n
{n,}    aspoň n
{n,m}   od n do m
[]      množina znaků
[^]     negace množiny
()      skupina
|       nebo
```

---

## 13.17) Escapování speciálních znaků

Některé znaky mají speciální význam:
- `.`
- `+`
- `*`
- `?`
- `(`
- `)`
- `[`
- `]`
- `{`
- `}`
- `|`
- `^`
- `$`

Když chceš hledat opravdu tento znak, musíš použít `\`.

Např. skutečná tečka:

```powershell
"verze 1.0" -match "\."
# True
```

Skutečné plus:

```powershell
"a+b" -match "\+"
# True
```

---

## 13.18) Časté chyby

### 1. Zapomenuté kotvy

```powershell
"abc123" -match "\d+"
# True
```

To neznamená „je to číslo“.
Jen „obsahuje číslo“.

Správně:

```powershell
"abc123" -match "^\d+$"
# False
```

### 2. Tečka není skutečná tečka

```powershell
"a.b" -match "."
# True
```

Tečka znamená libovolný znak.

Skutečná tečka:

```powershell
"a.b" -match "\."
# True
```

### 3. `\w` není jen písmeno

`\w` zahrnuje i:
- čísla
- `_`

Takže:

```powershell
"abc_123" -match "^\w+$"
# True
```

---

## 13.19) Doporučený postup při tvorbě regexu

Když si regex skládáš:
1. Rozmysli si, **co přesně chceš ověřit**
2. Rozhodni, jestli hledáš:
   - jen část textu
   - nebo celý text
3. Začni jednoduše
4. Postupně přidávej:
   - kotvy `^ $`
   - množiny `[]`
   - četnosti `+ * ? {n,m}`
   - skupiny `()`

---

## 13.20) Příklady od jednoduchých ke složitějším

### Obsahuje číslo
```powershell
$text = "abc5def"
$text -match "\d"
# True
```

### Obsahuje aspoň 3 číslice za sebou
```powershell
"text123x" -match "\d{3}"
# True
```

### Je to jen malé písmeno a čísla
```powershell
"abc123" -match "^[a-z0-9]+$"
# True
```

### Začíná velkým písmenem a pak malá
```powershell
"Karel" -match "^[A-Z][a-z]+$"
# True
```

### Jednoduché datum `dd.mm.rrrr`
```powershell
"13.03.2026" -match "^\d{2}\.\d{2}\.\d{4}$"
# True
```
