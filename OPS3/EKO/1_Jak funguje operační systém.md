## Co dělá operační systém

Mezi hardwarem a aplikací, kterou spustíš, je vrstva, která rozděluje prostředky a hlídá, aby si programy nelezly do zelí.

```
     aplikace          Word, prohlížeč, hra
  ─────────────────────────────────────────
   uživatelský režim   knihovny, příkazová řádka
  ─────────────────────────────────────────
     režim jádra       správa procesů, paměti,
                       souborů, zařízení, sítě
  ─────────────────────────────────────────
       hardware        procesor, RAM, disk
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

Ve Windows se jádru říká **NT kernel** a je v souboru `ntoskrnl.exe`.

## Systémové volání

Když program potřebuje něco, na co sám nemá právo — otevřít soubor, poslat data po síti, vytvořit proces — **požádá o to jádro**. Té žádosti se říká systémové volání.

Co se stane, když program čte soubor:

1. Program zavolá funkci z rozhraní Windows API
2. Ta se přes knihovnu `ntdll.dll` změní na systémové volání
3. Procesor přepne do režimu jádra 
4. Jádro ověří, jestli má program na soubor právo
5. Jádro přečte data z disku a předá je zpět
6. Procesor přepne zpátky do uživatelského režimu

Proto se program nemůže „prostě podívat" na cizí soubor. Kontrolu práv dělá jádro při každém přístupu a obejít se nedá.

## Rodiny operačních systémů

| Systém | Jádro | Kde běží |
| --- | --- | --- |
| Windows | NT, hybridní | stolní počítače, notebooky, servery |
| Linux | Linux, monolitické | servery, vestavěná zařízení, Android |
| macOS | XNU, hybridní | počítače Apple |
| Android | Linux | telefony a tablety |

Jádro je to, co odlišuje systémy nejvíc. Zbytek — grafické prostředí, programy, vzhled — se dá vyměnit.

## BIOS a UEFI

**BIOS** je firmware ze 70. let. Umí toho málo a několik jeho omezení dnes vadí.

**UEFI** ho od roku 2005 nahrazuje. Není to jen novější BIOS — je to malý operační systém, který umí číst souborový systém, má vlastní ovladače a spouští programy ve formátu `.efi`.