## Co dělá operační systém

Mezi hardwarem a aplikací, kterou spustíš, je vrstva, která rozděluje prostředky a hlídá, aby si programy nelezly do zelí.

```
     aplikace          firefox, ls, hra
  ─────────────────────────────────────
   uživatelský prostor  knihovny, shell
  ─────────────────────────────────────
        jádro           procesy, paměť,
                        soubory, zařízení, síť
  ─────────────────────────────────────
       hardware         procesor, RAM, disk
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

## Linux není operační systém

Linux je **jádro**. Napsal ho Linus Torvalds v roce 1991 jako student a vydal pod licencí GPL.

Samotné jádro je k ničemu - potřebuje knihovny, shell a nástroje. Ty vznikly dřív v projektu GNU. Proto se celku někdy říká **GNU/Linux**.

### Co je distribuce

Distribuce je to, co si opravdu stáhneš a nainstaluješ:

- jádro Linux
- systémové knihovny
- balíčkovací nástroj a repozitáře
- instalátor
- výchozí konfigurace a výběr programů
- podpora a aktualizace na nějakou dobu dopředu

| Rodina | Balíčky | Nástroj | Zástupci |
| --- | --- | --- | --- |
| Debian | `.deb` | `apt` | Debian, Ubuntu, Mint |
| Red Hat | `.rpm` | `dnf` | Fedora, RHEL, Rocky |
| Arch | `.pkg.tar.zst` | `pacman` | Arch, Manjaro |
| SUSE | `.rpm` | `zypper` | openSUSE |

Rodina určuje, jak se instaluje software a kde hledat nápovědu. Návod psaný pro Ubuntu obvykle nefunguje na Fedoře doslova.

**LTS** znamená Long Term Support, tedy vydání s dlouhou podporou. Ubuntu LTS vychází jednou za dva roky a má podporu pět let. Na server se nasazuje skoro vždy LTS - nechceš, aby ti po devíti měsících přestaly chodit aktualizace.

## Uživatelský režim a režim jádra

Procesor umí běžet ve dvou režimech.

| Režim | Kdo tam běží | Co smí |
| --- | --- | --- |
| Režim jádra | jádro, ovladače | všechno |
| Uživatelský režim | aplikace | skoro nic |

Aplikace běží vždy v uživatelském režimu. To je záměr - kdyby směla všechno, jedna chyba v programu by položila celý systém.

Na procesorech x86 se těmto úrovním říká **ochranné kruhy** (rings) a jsou čtyři, `ring 0` až `ring 3`. Prakticky se používají jen dva: `ring 0` pro jádro a `ring 3` pro aplikace.

## Systémové volání

Když program potřebuje něco, na co sám nemá právo - otevřít soubor, poslat data po síti, vytvořit proces - **požádá o to jádro**. Té žádosti se říká systémové volání.

Co se stane, když program čte soubor:

1. Program zavolá `open()` a předá cestu k souboru
2. Procesor přepne do režimu jádra
3. Jádro ověří, jestli má program na soubor právo
4. Jádro přečte data z disku a předá je zpět
5. Procesor přepne zpátky do uživatelského režimu

Nejběžnější systémová volání:

| Volání | K čemu je |
| --- | --- |
| `open`, `read`, `write`, `close` | práce se soubory |
| `fork`, `execve`, `exit` | vytvoření a spuštění procesu |
| `mmap` | přidělení paměti |
| `socket`, `connect` | síťová komunikace |

Proto se program nemůže „prostě podívat" na cizí soubor. Kontrolu práv dělá jádro při každém přístupu a obejít se nedá.

## Architektury jader

| Typ | Kde běží ovladače | Zástupci |
| --- | --- | --- |
| Monolitické | v režimu jádra | Linux, BSD |
| Mikrojádro | v uživatelském prostoru | Minix, QNX, seL4 |
| Hybridní | většina v jádře, část mimo | Windows NT, macOS |

Linux je monolitický **s moduly** - ovladače se dají zavádět za běhu, takže se nemusí překládat celé jádro.

Kompromis je jasný: rychlost proti izolaci chyb. **Ovladač v `ring 0` nemá žádnou izolaci** - má stejná práva jako jádro samo. Chyba v ovladači proto neshodí jeden program, ale celý systém.

## Co se děje po zapnutí počítače

| Krok | Co se stane |
| --- | --- |
| 1. Napájení a POST | firmware otestuje procesor, paměť a základní zařízení |
| 2. UEFI | inicializuje hardware a načte seznam zaváděcích položek |
| 3. ESP | najde na disku systémový oddíl EFI a v něm soubor zavaděče |
| 4. Zavaděč (GRUB) | zobrazí nabídku systémů, načte jádro a initramfs do paměti |
| 5. Jádro | rozbalí se, spustí ovladače, připojí dočasný kořenový systém |
| 6. initramfs | dodá ovladače potřebné k připojení skutečného disku |
| 7. Přepnutí kořene | jádro přepne na skutečný souborový systém |
| 8. init (systemd) | první proces, PID 1, spustí služby |
| 9. Přihlášení | textová výzva nebo grafický přihlašovací správce |

**Zavaděč není součást systému.** Je to samostatný program, který systém teprve najde, načte do paměti, předá mu řízení - a skončí.

### ESP

**EFI System Partition** je malý oddíl naformátovaný na FAT32, obvykle 100 až 500 MB. Leží v něm zavaděče všech nainstalovaných systémů:

```
ESP/
└── EFI/
    ├── Microsoft/Boot/bootmgfw.efi     zavaděč Windows
    ├── ubuntu/grubx64.efi              zavaděč Linuxu
    └── BOOT/BOOTX64.EFI                záložní
```

Proto jde mít na jednom disku Windows i Linux vedle sebe - každý si dá do ESP vlastní složku a nešlapou si po sobě.

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

**Secure Boot** kontroluje podpis zavaděče. Brání tomu, aby se mezi firmware a jádro vecpal škodlivý kód.