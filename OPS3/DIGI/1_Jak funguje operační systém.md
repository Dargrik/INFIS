## Co dělá operační systém

Mezi hardwarem a aplikací, kterou spustíš, je vrstva, která rozděluje prostředky a hlídá, aby si programy nelezly do zelí.

```
     aplikace          firefox, ls, hra
  ─────────────────────────────────────
   uživatelský prostor  knihovny, shell
  ─────────────────────────────────────
        jádro           správa procesů, paměti,
                        souborů, zařízení, sítě
  ─────────────────────────────────────
       hardware         procesor, RAM, disk
```

Jádro má čtyři hlavní úkoly:

| Úkol | Co to znamená |
| --- | --- |
| Správa procesů | rozhoduje, který program má kdy procesor |
| Správa paměti | přiděluje paměť a hlídá, aby program nečetl cizí data |
| Souborové systémy | překládá „soubor" na konkrétní bloky na disku |
| Ovladače | mluví s konkrétním hardwarem |

## Uživatelský režim a režim jádra

Procesor umí běžet ve dvou režimech. V **režimu jádra** smí program všechno — sáhnout na hardware, na jakoukoli paměť. V **uživatelském režimu** nesmí skoro nic.

Aplikace běží vždy v uživatelském režimu. To je záměr: kdyby smět mohla všechno, jedna chyba v programu by položila celý systém.

## Systémové volání

Když program potřebuje něco, na co sám nemá právo — otevřít soubor, poslat data po síti, vytvořit proces — **požádá o to jádro**. Té žádosti se říká systémové volání.

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

Vidět se to dá příkazem `strace`, který vypíše všechna systémová volání programu:

```
strace ls
```

## Linux není operační systém

Linux je **jádro**. Napsal ho Linus Torvalds v roce 1991 jako student a vydal pod licencí GPL.

Samotné jádro je k ničemu — potřebuje k sobě knihovny, shell a nástroje. Ty vznikly dřív v projektu GNU. Proto se celku někdy říká **GNU/Linux**.

## Co je distribuce

Distribuce je to, co si opravdu stáhneš a nainstaluješ. Skládá se z:

- jádra Linux
- systémových knihoven
- balíčkovacího nástroje a repozitářů
- instalátoru
- výchozí konfigurace a výběru programů
- podpory a aktualizací na nějakou dobu dopředu

| Rodina | Balíčky | Nástroj | Zástupci |
| --- | --- | --- | --- |
| Debian | `.deb` | `apt` | Debian, Ubuntu, Mint |
| Red Hat | `.rpm` | `dnf` | Fedora, RHEL, Rocky |
| Arch | `.pkg.tar.zst` | `pacman` | Arch, Manjaro |
| SUSE | `.rpm` | `zypper` | openSUSE |

Rodina určuje, jak se instaluje software a kde se hledá nápověda. Návod psaný pro Ubuntu obvykle nefunguje na Fedoře doslova.

### LTS

**Long Term Support** — vydání s dlouhou podporou. Ubuntu LTS vychází jednou za dva roky a má podporu pět let. Ve firmě se nasazuje skoro vždy LTS: nechceš, aby ti server po devíti měsících přestal dostávat aktualizace.

Verzi zjistíš příkazem:

```
lsb_release -a
cat /etc/os-release
```

## BIOS a UEFI

**BIOS** je firmware ze 70. let. Umí toho málo a několik jeho omezení dnes vadí.

**UEFI** ho od roku 2005 nahrazuje. Není to jen novější BIOS — je to malý operační systém, který umí číst souborový systém, má vlastní ovladače a spouští programy ve formátu `.efi`.