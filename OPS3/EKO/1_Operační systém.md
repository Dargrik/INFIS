## Co dělá operační systém

Mezi hardwarem a aplikací, kterou spustíš, je vrstva, která rozděluje prostředky a hlídá, aby si programy nelezly do zelí.

```
     aplikace          Word, prohlížeč, hra
  ─────────────────────────────────────────
   uživatelský režim   knihovny, příkazová řádka
  ─────────────────────────────────────────
     režim jádra       procesy, paměť,
                       soubory, zařízení, síť
  ─────────────────────────────────────────
       hardware        procesor, RAM, disk
```

Jádro má čtyři hlavní úkoly.

| Úkol | Co to znamená |
| --- | --- |
| Správa procesů | rozhoduje, který program má kdy procesor |
| Správa paměti | přiděluje paměť a hlídá, aby program nečetl cizí data |
| Souborové systémy | překládá „soubor" na konkrétní bloky na disku |
| Ovladače | mluví s konkrétním hardwarem |

Když pustíš dva programy na jednom jádru procesoru, neběží současně. **Střídají se** a to střídání řídí jádro.

## Typy operačních systémů

Dělí se podle několika hledisek najednou.

| Hledisko | Varianty |
| --- | --- |
| Počet úloh | jednoúlohové (MS-DOS), víceúlohové (vše dnešní) |
| Počet uživatelů | jednouživatelské, víceuživatelské |
| Odezva | běžné, systémy reálného času (řídicí jednotky, lékařské přístroje) |
| Nasazení | pracovní stanice, server, vestavěné, mobilní |

**Systém reálného času** nemusí být rychlý. Musí být **předvídatelný** - musí zaručit, že odpoví do dané doby. Airbag potřebuje odpověď do milisekundy vždy, ne obvykle.

## Rodiny operačních systémů

| Systém | Jádro | Kde běží |
| --- | --- | --- |
| Windows | NT, hybridní | stolní počítače, notebooky, servery |
| Linux | Linux, monolitické | servery, vestavěná zařízení, Android |
| macOS | XNU, hybridní | počítače Apple |
| Android | Linux | telefony a tablety |

Jádro je to, co systémy odlišuje nejvíc. Vzhled, programy a prostředí se dají vyměnit.

## Windows NT

**New Technology.** Microsoft na něm začal pracovat v roce 1988 a najal si na to Davea Cutlera, který předtím v DEC vedl vývoj systému VMS. Proto Windows NT nese myšlenky z minipočítačového světa, ne z DOSu.

První vydání bylo **Windows NT 3.1 v roce 1993**. Napsané od nuly, 32bitové, s preemptivním multitaskingem, chráněnou pamětí a bezpečnostním modelem.

Dlouho běžely dvě větve vedle sebe - NT pro firmy a servery, a domácí řada nad DOSem (Windows 3.x, 95, 98, ME). **Spojily se ve Windows XP v roce 2001.**

Značka NT z názvu zmizela, ale jádro zůstalo. **Windows 11 je Windows NT** a hlásí se jako verze 10.0.

### Vrstvy Windows

| Vrstva | Kde běží | Co dělá |
| --- | --- | --- |
| Aplikace | uživatelský režim | Word, prohlížeč |
| `ntdll.dll` | uživatelský režim | převádí volání na systémová volání |
| Executive | režim jádra | objekty, paměť, procesy, I/O, bezpečnost |
| Kernel (KE) | režim jádra | plánování vláken, přerušení |
| Ovladače | režim jádra | souborové systémy, grafika, zařízení |
| HAL (`hal.dll`) | režim jádra | odstiňuje konkrétní základní desku |

Executive i Kernel jsou v jednom souboru `ntoskrnl.exe`.

**Ve Windows je všechno objekt** - proces, vlákno, soubor, klíč registru. Přistupuje se k nim přes takzvaný handle. V Linuxu platí naopak „všechno je soubor".

## Linux jen v přehledu

Linux je **jádro**, ne operační systém. Torvalds, 1991, licence GPL. To, co se instaluje, je **distribuce**: jádro + knihovny + balíčkovač + repozitáře + instalátor + konfigurace + podpora.

| Rodina | Balíčky | Nástroj | Zástupci |
| --- | --- | --- | --- |
| Debian | `.deb` | `apt` | Debian, Ubuntu, Mint |
| Red Hat | `.rpm` | `dnf` | Fedora, RHEL, Rocky |
| Arch | `.pkg.tar.zst` | `pacman` | Arch, Manjaro |

**LTS** znamená vydání s dlouhou podporou. Ubuntu LTS vychází jednou za dva roky, podpora pět let.

Windows to má obdobně: běžné vydání proti verzi **LTSC**, která se nasazuje tam, kde nesmí nic měnit vzhled ani chování - na pokladny, do přístrojů, na bankomaty.

## Uživatelský režim a režim jádra

Procesor umí běžet ve dvou režimech.

| Režim | Kdo tam běží | Co smí |
| --- | --- | --- |
| Režim jádra | jádro, ovladače | všechno |
| Uživatelský režim | aplikace | skoro nic |

Aplikace běží vždy v uživatelském režimu. To je záměr - kdyby směla všechno, jedna chyba v programu by položila celý systém.

Na procesorech x86 se těmto úrovním říká **ochranné kruhy** (rings) a jsou čtyři, `ring 0` až `ring 3`. Prakticky se používají jen dva: `ring 0` pro jádro a `ring 3` pro aplikace.

**Ovladač v `ring 0` nemá žádnou izolaci** - má stejná práva jako jádro samo. Proto je modrá obrazovka skoro vždycky ovladač, ne aplikace.

Nejznámější příklad: 19. července 2024 vydala bezpečnostní firma CrowdStrike vadný soubor pro svůj ovladač běžící v `ring 0`. Spadlo přes osm milionů počítačů po celém světě - letiště, nemocnice, banky. Kdyby ta věc běžela v uživatelském režimu, restartoval by se jeden proces.

## Systémové volání

Když program potřebuje něco, na co sám nemá právo - otevřít soubor, poslat data po síti, vytvořit proces - **požádá o to jádro**. Té žádosti se říká systémové volání.

Co se stane, když program čte soubor:

1. Program zavolá funkci z rozhraní Windows API
2. Ta se přes knihovnu `ntdll.dll` změní na systémové volání
3. Procesor přepne do režimu jádra
4. Jádro ověří, jestli má program na soubor právo
5. Jádro přečte data z disku a předá je zpět
6. Procesor přepne zpátky do uživatelského režimu

Proto se program nemůže „prostě podívat" na cizí soubor. Kontrolu práv dělá jádro při každém přístupu a obejít se nedá.

## Architektury jader

| Typ | Kde běží ovladače | Zástupci |
| --- | --- | --- |
| Monolitické | v režimu jádra | Linux, BSD |
| Mikrojádro | v uživatelském prostoru | Minix, QNX, seL4 |
| Hybridní | většina v jádře, část mimo | Windows NT, macOS |

Windows je **hybridní**. Návrhem vychází z mikrojádra - vrstvy, oddělené podsystémy - ale kvůli výkonu skoro všechno běží v režimu jádra. Z hlediska struktury je to mikrojádro, z hlediska izolace se to chová jako monolit.

## Co se děje po zapnutí počítače

| Krok | Co se stane |
| --- | --- |
| 1. Napájení a POST | firmware otestuje procesor, paměť a základní zařízení |
| 2. UEFI | inicializuje hardware a načte seznam zaváděcích položek |
| 3. ESP | najde na disku systémový oddíl EFI a v něm zavaděč |
| 4. Windows Boot Manager | `bootmgfw.efi` - nabídka, když je systémů víc |
| 5. `winload.efi` | načte do paměti jádro a základní ovladače |
| 6. Jádro NT | spustí se, načte zbytek ovladačů a registr |
| 7. `smss.exe` | správce relací, připraví prostředí |
| 8. `winlogon.exe` | přihlašovací obrazovka |
| 9. `explorer.exe` | plocha, nabídka Start |

**Zavaděč není součást systému.** Je to samostatný program, který systém teprve najde, načte do paměti, předá mu řízení - a skončí.

Zaváděcí konfiguraci vypíšeš příkazem:

```
bcdedit /enum
```

### ESP

**EFI System Partition** je malý oddíl naformátovaný na FAT32, obvykle 100 až 500 MB. Leží v něm zavaděče všech nainstalovaných systémů:

```
ESP/
└── EFI/
    ├── Microsoft/Boot/bootmgfw.efi     zavaděč Windows
    ├── ubuntu/grubx64.efi              zavaděč Linuxu
    └── BOOT/BOOTX64.EFI                záložní
```

Proto jde mít na jednom disku Windows i Linux vedle sebe.

Podívat se do něj můžeš takto (příkazová řádka jako správce):

```
mountvol S: /s
dir S:\EFI
```

## BIOS a UEFI

**BIOS** je firmware ze 70. let. **UEFI** ho od roku 2005 nahrazuje. Není to jen novější BIOS - je to malý operační systém, který umí číst souborový systém a spouštět programy ve formátu `.efi`.

| | BIOS | UEFI |
| --- | --- | --- |
| Tabulka oddílů | MBR | GPT |
| Největší disk | 2 TB | prakticky bez omezení |
| Počet oddílů | 4 primární | 128 |
| Kde je zavaděč | první sektor disku, 512 B | soubor `.efi` na oddílu ESP |
| Secure Boot | není | je |
| Ovládání | text a klávesnice | grafika a myš |

**Secure Boot** kontroluje podpis zavaděče. Windows 11 ho vyžaduje spolu s čipem TPM 2.0.

Jestli tvůj počítač běží v režimu UEFI, zjistíš v nástroji `msinfo32` v položce **Režim systému BIOS**.