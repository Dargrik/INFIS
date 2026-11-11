# Přehled příkazů

## Orientace

| Příkaz | Ukázka |
| --- | --- |
| `pwd` | `pwd` |
| `ls` | `ls`<br>`ls -l`<br>`ls -a`<br>`ls -la`<br>`ls /etc` |
| `cd` | `cd /etc`<br>`cd ..`<br>`cd ~`<br>`cd -`<br>`cd` |
| `clear` | `clear` |
| `exit` | `exit` |
| `whoami` | `whoami` |
| `hostname` | `hostname` |
| `date` | `date`<br>`date +%F`<br>`date +%Y-%m-%d` |
| `uname` | `uname -a` |
| `lsb_release` | `lsb_release -a` |
| `history` | `history`<br>`history \| tail -20` |

## Nápověda

| Příkaz | Ukázka |
| --- | --- |
| `man` | `man ls`<br>`man 5 passwd` |
| `apropos` | `apropos copy` |
| `--help` | `ls --help` |
| `type` | `type ls`<br>`type -a ls`<br>`type cd` |
| `which` | `which ls` |
| `whereis` | `whereis ls` |
| `command -v` | `command -v ls` |

## Soubory a adresáře

| Příkaz | Ukázka |
| --- | --- |
| `mkdir` | `mkdir pokus`<br>`mkdir -p a/b/c` |
| `rmdir` | `rmdir pokus` |
| `touch` | `touch soubor.txt` |
| `cp` | `cp a.txt b.txt`<br>`cp -r slozka zaloha` |
| `mv` | `mv a.txt b.txt`<br>`mv a.txt ~/Dokumenty/` |
| `rm` | `rm soubor.txt`<br>`rm -r slozka`<br>`rm -i soubor.txt` |
| `file` | `file /bin/ls`<br>`file soubor.txt` |
| `stat` | `stat soubor.txt`<br>`stat -c%s soubor.txt` |

## Obsah souborů

| Příkaz | Ukázka |
| --- | --- |
| `cat` | `cat soubor.txt`<br>`cat a.txt b.txt` |
| `less` | `less /etc/passwd` |
| `head` | `head -3 /etc/passwd` |
| `tail` | `tail -3 /etc/passwd`<br>`tail -n +2 data.csv`<br>`tail -f /var/log/syslog` |
| `nano` | `nano soubor.txt` |
| `wc` | `wc -l soubor.txt`<br>`wc -w soubor.txt`<br>`wc -c soubor.txt` |

## Proměnné, aliasy a PATH

| Příkaz | Ukázka |
| --- | --- |
| `echo` | `echo "text"`<br>`echo $HOME`<br>`echo $PATH`<br>`echo $?` |
| `export` | `export PROJEKT=~/projekty`<br>`export PATH="$HOME/bin:$PATH"` |
| `alias` | `alias`<br>`alias ll='ls -la'` |
| `unalias` | `unalias ll` |
| `source` | `source ~/.bashrc` |
| `$( )` | `dnes=$(date +%F)`<br>`echo "Polozek: $(ls \| wc -l)"` |
| `$(( ))` | `echo $((10 + 3))`<br>`echo $((pocet + 1))` |
| `test` | `test -f ~/.bashrc && echo je`<br>`test -d ~/bin \|\| mkdir ~/bin` |

## Spojování příkazů

| Zápis | Ukázka |
| --- | --- |
| `;` | `cd ~ ; ls` |
| `&&` | `mkdir test && cd test` |
| `\|\|` | `cd /nic \|\| echo "neni"` |
| `$?` | `neexistujici`<br>`echo $?` |

## Přesměrování a roura

| Zápis | Ukázka |
| --- | --- |
| `>` | `ls /etc > seznam.txt` |
| `>>` | `date >> log.txt` |
| `2>` | `ls /etc /nic 2> chyby.txt` |
| `&>` | `ls /etc /nic &> vse.txt` |
| `2> /dev/null` | `find / -name "*.conf" 2> /dev/null` |
| `2>&1` | `ls /etc /nic > vse.txt 2>&1` |
| `<` | `wc -l < /etc/passwd` |
| `\|` | `ls /usr/bin \| wc -l` |
| `tee` | `ls /etc \| tee seznam.txt \| wc -l` |

## Filtry

| Příkaz | Ukázka |
| --- | --- |
| `grep` | `grep bash /etc/passwd`<br>`grep -i BASH /etc/passwd`<br>`grep -v bash /etc/passwd`<br>`grep -n bash /etc/passwd`<br>`grep -c bash /etc/passwd`<br>`grep '^root' /etc/passwd`<br>`grep 'nologin$' /etc/passwd` |
| `cut` | `cut -d: -f1 /etc/passwd`<br>`cut -d, -f2 data.csv`<br>`cut -c1-5 soubor.txt` |
| `sort` | `sort jmena.txt`<br>`sort -n cisla.txt`<br>`sort -r jmena.txt`<br>`sort -u jmena.txt`<br>`sort -h`<br>`sort -t, -k3 -n data.csv` |
| `uniq` | `sort jmena.txt \| uniq`<br>`sort jmena.txt \| uniq -c`<br>`sort jmena.txt \| uniq -d` |
| `tr` | `echo "ahoj" \| tr a-z A-Z`<br>`echo "a:b" \| tr ':' '\n'`<br>`echo "a  b" \| tr -s ' '` |
| `printf` | `printf "%4d" 42`<br>`printf "9\n10\n2\n"` |

## Vyhledávání

| Příkaz | Ukázka |
| --- | --- |
| `find` | `find . -name "*.log"`<br>`find . -iname "*.LOG"`<br>`find . -type f`<br>`find . -type d`<br>`find . -size +1M`<br>`find . -mtime -7`<br>`find . -maxdepth 1`<br>`find . -empty`<br>`find /home -user student` |
| `find` s akcí | `find . -name "*.tmp" -delete`<br>`find . -name "*.sh" -exec chmod +x {} \;`<br>`find . -name "*.txt" -exec wc -l {} +` |
| `find` s kombinací | `find . -name "*.jpg" -o -name "*.png"`<br>`find . -type f ! -name "*.txt"`<br>`find . \( -name "*.jpg" -o -name "*.png" \) -size +1M` |
| `xargs` | `find . -name "*.log" \| xargs rm`<br>`find . -name "*.txt" -print0 \| xargs -0 ls -l` |
| `locate` | `locate heslo.txt`<br>`locate -i readme` |
| `updatedb` | `sudo updatedb` |
| `grep -r` | `grep -r "PATH" /etc`<br>`grep -rl "PATH" /etc`<br>`grep -rn --include="*.sh" "exit" ~` |

## Disk a místo

| Příkaz | Ukázka |
| --- | --- |
| `df` | `df -h` |
| `du` | `du -sh ~`<br>`du -h --max-depth=1 ~`<br>`du -h --max-depth=1 ~ \| sort -h \| tail -5` |

## Balíčky a práva

| Příkaz | Ukázka |
| --- | --- |
| `sudo` | `sudo updatedb` |
| `apt` | `sudo apt update && sudo apt upgrade`<br>`sudo apt install plocate` |
| `chmod` | `chmod +x skript.sh` |

## Skripty

| Příkaz | Ukázka |
| --- | --- |
| `#!/bin/bash` | první řádek souboru |
| `bash` | `bash skript.sh` |
| `./` | `./skript.sh` |
| `source` | `source skript.sh` |
| `bash -x` | `bash -x skript.sh` |
| `bash -n` | `bash -n skript.sh` |
| `set -u` | `set -u` |
| `read` | `read jmeno`<br>`read -p "Jmeno: " jmeno`<br>`read -p "Heslo: " -s heslo`<br>`read -p "Dal? " -n 1 odpoved` |
| `exit` | `exit 0`<br>`exit 1` |
| `>&2` | `echo "Chyba" >&2` |
| `sleep` | `sleep 5` |

| Zápis | Ukázka |
| --- | --- |
| `$0` | `echo "Skript: $0"` |
| `$1` | `echo "Prvni: $1"` |
| `$#` | `echo "Pocet: $#"` |
| `$@` | `echo "Vsechny: $@"` |
| `$$` | `echo "Proces: $$"` |

## Konstrukce ve skriptu

| Zápis | Ukázka |
| --- | --- |
| `if` | `if [ -f soubor ]; then echo je; fi` |
| `if else` | `if [ -d "$c" ]; then ls "$c"; else mkdir "$c"; fi` |
| `elif` | `if [ "$a" = 1 ]; then ls; elif [ "$a" = 2 ]; then date; fi` |
| `case` | `case "$1" in start) ls ;; stop) date ;; *) exit 1 ;; esac` |
| `for` | `for i in 1 2 3; do echo $i; done`<br>`for s in *.txt; do wc -l "$s"; done`<br>`for i in {1..10}; do echo $i; done`<br>`for ((i=1; i<=10; i++)); do echo $i; done` |
| `while` | `while [ "$i" -le 5 ]; do echo $i; i=$((i + 1)); done` |
| `while read` | `while read radek; do echo "$radek"; done < soubor.txt`<br>`while IFS=: read jm _ uid _; do echo "$jm $uid"; done < /etc/passwd` |
| `until` | `until ping -c 1 seznam.cz &> /dev/null; do sleep 5; done` |
| `break` | `break` |
| `continue` | `[ -f "$s" ] \|\| continue` |

## Testy v podmínce

| Zápis | Ukázka |
| --- | --- |
| `-e` | `[ -e cokoli ]` |
| `-f` | `[ -f soubor.txt ]` |
| `-d` | `[ -d ~/zalohy ]` |
| `-r` `-w` `-x` | `[ -x skript.sh ]` |
| `-s` | `[ -s soubor.txt ]` |
| `-z` | `[ -z "$1" ]` |
| `-n` | `[ -n "$1" ]` |
| `=` `!=` | `[ "$a" = "start" ]` |
| `-eq` `-ne` | `[ "$a" -eq 5 ]` |
| `-gt` `-ge` | `[ "$pocet" -gt 100 ]` |
| `-lt` `-le` | `[ "$pocet" -le 10 ]` |
| `!` | `[ ! -d "$cil" ]` |
| `[[ ]]` | `[[ "$jmeno" == zaloha* ]]` |
| `(( ))` | `(( pocet > 100 ))` |

## Zástupné znaky

| Zápis | Ukázka |
| --- | --- |
| `*` | `ls *.txt` |
| `?` | `ls soubor?.txt` |
| `[ ]` | `ls soubor[123].txt` |
| `{ }` | `mkdir -p cviceni/{a,b,c}` |
| `~` | `cd ~` |
| `'` | `grep '^root' /etc/passwd` |
| `"` | `cd "$cesta"` |
| `\` | `touch moje\ poznamky.txt` |

## Klávesové zkratky

| Zkratka | K čemu |
| --- | --- |
| <kbd>Tab</kbd> | doplnění |
| <kbd>Ctrl</kbd>+<kbd>C</kbd> | přerušení |
| <kbd>Ctrl</kbd>+<kbd>D</kbd> | konec vstupu |
| <kbd>Ctrl</kbd>+<kbd>L</kbd> | smazání obrazovky |
| <kbd>Ctrl</kbd>+<kbd>R</kbd> | hledání v historii |
| <kbd>šipka nahoru</kbd> | předchozí příkaz |
