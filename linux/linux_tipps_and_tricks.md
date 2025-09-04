---
title: "Tipps und Tricks"
---

[← Zurück zu Linux](./)

[← Zurück zur Startseite](../)

# Linux Tipps und Tricks

## Allgemeine Links

*	[Diverse LINUX TUTORIALS](https://www.techgrube.de/category/tutorials/tutorials-linux)
*	[Bootzeit mit Systemd analysieren](https://www.techgrube.de/tutorials/bootzeit-mit-systemd-analysieren)
*	[How to get total number of Threads](https://askubuntu.com/questions/88972/how-to-get-from-terminal-total-number-of-threads-per-process-and-total-for-al)
*	[How can I monitor the Thread count of a process on Linux](https://stackoverflow.com/questions/268680/how-can-i-monitor-the-thread-count-of-a-process-on-linux)


## Startup-Scripte `.bashrc`, `.profile`, `.bash_profile`

### Artikel und Links

*	[Bash Manpage (https://linux.die.net/man/1/bash)](https://linux.die.net/man/1/bash)
*	[Difference between bash_profile and bashrc](https://stackoverflow.com/questions/415403/whats-the-difference-between-bashrc-bash-profile-and-environment)
*	[Execution sequence for .bash_profile, .bashrc, .bash_login, .profile and .bash_logout](https://www.thegeekstuff.com/2008/10/execution-sequence-for-bash_profile-bashrc-bash_login-profile-and-bash_logout/)

### Informationen

#### Execution sequence for interactive login shell:

```
execute /etc/profile
IF ~/.bash_profile exists THEN
    execute ~/.bash_profile
ELSE
    IF ~/.bash_login exist THEN
        execute ~/.bash_login
    ELSE
        IF ~/.profile exist THEN
            execute ~/.profile
        END IF
    END IF
END IF
```

#### When you logout of the interactive shell, following is the sequence of execution:

```
IF ~/.bash_logout exists THEN
    execute ~/.bash_logout
END IF
```

#### Execution sequence for interactive non-login shell

```
IF ~/.bashrc exists THEN
    execute ~/.bashrc
END IF
```

#### Execution sequence from ETX with KDE-Desktop-profile:

```
execute /etc/profile
execute /etc/bashrc
/usr/bin/bash --login /usr/bin/startkde
execute /etc/profile
IF ~/.bash_profile exists THEN
    execute ~/.bash_profile
ELSE
    IF ~/.bash_login exist THEN
        execute ~/.bash_login
    ELSE
        IF ~/.profile exist THEN
            execute ~/.profile
        END IF
    END IF
END IF
```

#### Open Terminal within KDE-Desktop:

UseCase: interactive non-login

```
IF ~/.bashrc exists THEN
    execute ~/.bashrc
END IF
```

#### Connect to other host via `ssh`:

UseCase: interactive login

```
execute /etc/profile
IF ~/.bash_profile exists THEN
    execute ~/.bash_profile
ELSE
    IF ~/.bash_login exist THEN
        execute ~/.bash_login
    ELSE
        IF ~/.profile exist THEN
            execute ~/.profile
        END IF
    END IF
END IF
```


### Other Information

I have used Debian-family distros which appear to execute `.profile`, but not 
`.bash_profile`, whereas *RHEL* derivatives execute `.bash_profile` before 
`.profile`.


```
/bin/bash
       The bash executable
/etc/profile
       The systemwide initialization file, executed for login shells
~/.bash_profile
       The personal initialization file, executed for login shells
~/.bashrc
       The individual per-interactive-shell startup file
~/.bash_logout
       The individual login shell cleanup file, executed when a login shell exits
~/.inputrc
       Individual readline initialization file
```

Login shells are the ones that are read one you login (so, they are not 
executed when merely starting up xterm, for example).


### Check for 'Interactive Shell' or 'Login Shell' in Bash

```
$> [[ $- == *i* ]] && echo 'Interactive' || echo 'Not interactive'
$> shopt -q login_shell && echo 'Login shell' || echo 'Not login shell'
```

siehe auch [How to check if a shell is login/interactive/batch](https://unix.stackexchange.com/questions/26676/how-to-check-if-a-shell-is-login-interactive-batch)


## Zugriffsrechte 

## Dateien finden mit `find`

### Grundgerüst

Nachfolgendes Code-Fragment kann auch mit Leer- und Sonderzeichen in Dateinamen 
umgehen!

Finde nur Dateien im aktuellen Verzeichnis:

```
find $(pwd) -maxdepth 1 -type f -print0 | while read -d $'\0' ELEMENT; do
    echo "$ELEMENT"
done
```

Finde nur Verzeichnisse im aktuellen Verzeichnis:

```
find $(pwd) -maxdepth 1 -type d -print0 | while read -d $'\0' ELEMENT; do
    echo "$ELEMENT"
done
```

Finde nur Symbolische Links im aktuellen Verzeichnis:

```
find $(pwd) -maxdepth 1 -type l -print0 | while read -d $'\0' ELEMENT; do
    echo "$ELEMENT"
done
```


### Versteckte Dateien und Verzeichnisse

Finde nur **versteckte** Dateien:

```
find $(pwd) -maxdepth 1 -type f -print0 | while read -d $'\0' ELEMENT; do
    if [[ $ELEMENT == */.* ]]; then
        echo "$ELEMENT"
    fi
done
```

Finde nur **nicht versteckte** Dateien:

```
find $(pwd) -maxdepth 1 -type f -print0 | while read -d $'\0' ELEMENT; do
    if [[ $ELEMENT != */.* ]]; then
        echo "$ELEMENT"
    fi
done
```

Finde nur **versteckte Verzeichnisse**:

```
find $(pwd) -maxdepth 1 -type d -print0 | while read -d $'\0' ELEMENT; do
    if [[ $ELEMENT == */.* ]]; then
        echo "$ELEMENT"
    fi
done
```


### Dateien finden, die nach einem bestimmten Zeitpunkt modifiziert wurden

Ausschlaggebend ist der Vergleich der *modification time* von Dateien.

	$> ls -lt --full-time <path>

Find-Befehl:

	$> find <path> -type f -newermt "YYYY-MM-DD hh:mm:ss"

Beispiel: alle Dateien im Verzeichnis `./content`, die nach 
`2020-09-22 16:10:00` geändert wurden

	$> find ./content -type f -newermt "2020-09-22 16:10:00"

*	[Mit Python lösen](https://thispointer.com/python-get-last-modification-date-time-of-a-file-os-stat-os-path-getmtime/)

## System-Logs ansehen

Unter RedHat sind die System-Logs abgelegt unter:

	$> ls -l /run/log/journal/$(cat /etc/machine-id)/

Diese Dateien sind in einem Binärformat und können mit `journalctl` gelesen
werden:

	$> journalctl --file /run/log/journal/$(cat /etc/machine-id)/system.journal

Die Dateien werden von `logrotate` mitverwaltet und werden daher rolierend 
gewechselt.

Ein weiterer nützlicher Parameter ist `-f` für *follow*, um nur die aktuellesten
Meldungen (ähnlich dem `tail` Befehl) anzeigen zu lassen.

	$> journalctl -f --file /run/log/journal/$(cat /etc/machine-id)/system.journal

## `date`

*	aktuelles Datum formatiert ausgeben

		$> date +%Y%_m%_d
		
	Ausgabe `20201218`
		
*	aktuelle Uhrzeit formatiert ausgeben

		$> date +%H%M%S

	Ausgabe: `111358`


*	Kalenderwocke ausgeben

		$> date +%Y%_m%_d_%H%M%S_KW%V

	Ausgabe: `KW51`

*	alles zusammen (Datum, Uhrzeit, Kalenderwoche) ausgeben

		$> date +%Y%_m%_d_%H%M%S_KW%V

	Ausgabe: `20201218_111358_KW51`
	

## Bash "tests"

Link:

*	[The clasinc test command (https://wiki-dev.bash-hackers.org/commands/classictest)](https://wiki-dev.bash-hackers.org/commands/classictest)
*	[Advanced Bash Scripting Guide > Test Constructs (https://www.linuxtopia.org/online_books/advanced_bash_scripting_guide)](https://www.linuxtopia.org/online_books/advanced_bash_scripting_guide/testconstructs.html)

### File tests

Operator syntax       | Description                                                                     
--------------------- | --------------------------------------------------------------------------------
`-a <FILE>`           | True if `<FILE>` exists. (not recommended, may collide with -a for AND, see below)
`-e <FILE>`           | True if `<FILE>` exists.                                                          
`-f <FILE>`           | True, if `<FILE>` exists and is a **regular** file.                                   
`-d <FILE>`           | True, if `<FILE>` exists and is a **directory**.                                      
`-c <FILE>`           | True, if `<FILE>` exists and is a **character special** file.                         
`-b <FILE>`           | True, if `<FILE>` exists and is a **block special** file.                             
`-p <FILE>`           | True, if `<FILE>` exists and is a **named pipe** (FIFO).                              
`-S <FILE>`           | True, if `<FILE>` exists and is a **socket** file.                                    
`-L <FILE>`           | True, if `<FILE>` exists and is a **symbolic link**.                                  
`-h <FILE>`           | True, if `<FILE>` exists and is a **symbolic link**.                                  
`-g <FILE>`           | True, if `<FILE>` exists and has **sgid bit set**.                                    
`-u <FILE>`           | True, if `<FILE>` exists and has **suid bit set**.                                    
`-r <FILE>`           | True, if `<FILE>` exists and is **readable**.                                         
`-w <FILE>`           | True, if `<FILE>` exists and is **writable**.                                         
`-x <FILE>`           | True, if `<FILE>` exists and is **executable**.                                       
`-s <FILE>`           | True, if `<FILE>` exists and has size bigger than 0 (**not empty**).                  
`-t <fd>`             | True, if file descriptor `<fd>` is open and refers to a terminal.                 
`<FILE1> -nt <FILE2>` | True, if `<FILE1>` is **newer than** `<FILE2>` (mtime).                                 
`<FILE1> -ot <FILE2>` | True, if `<FILE1>` is **older than** `<FILE2>` (mtime).                                 
`<FILE1> -ef <FILE2>` | True, if `<FILE1>` and `<FILE2>` refer to the **same device and inode numbers**.        


### String tests

Operator syntax          | Description                                                                                                                       
------------------------ | ----------------------------------------------------------------------------------------------------------------------------------
`-z <STRING>`            | True, if `<STRING>` is **empty**.                                                                                                  
`-n <STRING>`            | True, if `<STRING>` is **not empty** (this is the default operation).                                                              
`<STRING1> = <STRING2>`  | True, if the strings are **equal**.                                                                                               
`<STRING1> != <STRING2>` | True, if the strings are **not equal**.                                                                                           
`<STRING1> < <STRING2>`  | True if `<STRING1>` **sorts before** `<STRING2>` lexicographically (pure ASCII, not current locale!). Remember to escape! Use `\<`
`<STRING1> > <STRING2>`  | True if `<STRING1>` **sorts after** `<STRING2>` lexicographically (pure ASCII, not current locale!). Remember to escape! Use `\>` 


### Arithmetic tests

Operator syntax             | Description                                                        
--------------------------- | -------------------------------------------------------------------
`<INTEGER1> -eq <INTEGER2>` | True, if the integers are **equal**.                               
`<INTEGER1> -ne <INTEGER2>` | True, if the integers are **NOT equal**.                           
`<INTEGER1> -le <INTEGER2>` | True, if the first integer is **less than or equal** second one.   
`<INTEGER1> -ge <INTEGER2>` | True, if the first integer is **greater than or equal** second one.
`<INTEGER1> -lt <INTEGER2>` | True, if the first integer is **less than** second one.            
`<INTEGER1> -gt <INTEGER2>` | True, if the first integer is **greater than** second one.    


### Misc syntax

Operator syntax      | Description                                                                                                                     
-------------------- | --------------------------------------------------------------------------------------------------------------------------------
`<TEST1> -a <TEST2>` | True, if `<TEST1>` **and** `<TEST2>` are true (AND). Note that -a also may be used as a file test (see above)                   
`<TEST1> -o <TEST2>` | True, if either `<TEST1>` **or** `<TEST2>` is true (OR).                                                                        
`! <TEST>`           | True, if `<TEST>` is **false** (NOT).                                                                                           
`( <TEST> )`         | Group a test (for precedence). **Attention**: In normal shell-usage, the "`(`" and "`)`" must be escaped; use "`\(`" and "`\)`"!
`-o <OPTION_NAME>`   | True, if the shell option `<OPTION_NAME>` is set.                                                                               
`-v <VARIABLENAME>`  | True if the variable `<VARIABLENAME>` has been set. Use `var[n]` for array elements.                                            
`-R <VARIABLENAME>`  | True if the variable `<VARIABLENAME>` has been set and is a nameref variable (since 4.3-alpha)   


### [AND and OR](https://wiki-dev.bash-hackers.org/commands/classictest#and_and_or)

### NOT

As for AND and OR, there are 2 ways to negate a test with the shell keyword `!` 
or passing `!` as an argument to `test`.

Here `!` negates the exit status of the command `test` which is 0 (true), and 
the else part is executed:

	if ! [ -d '/tmp' ]; then echo "/tmp doesn't exists"; else echo "/tmp exists"; fi

Here the `test` command itself exits with status 1 (false) and the else is also 
executed:

	if  [ ! -d '/tmp' ]; then echo "/tmp doesn't exists"; else echo "/tmp exists"; fi

Unlike for AND and OR, both methods for NOT have an identical behaviour, at 
least for doing one single test.


### [Pitfalls](https://wiki-dev.bash-hackers.org/commands/classictest#pitfalls_summarized)

## Daten von einem CheckMK-Client konsolidieren

	$> telnet cael341 6556 | gzip > cael341.gz
	$> gzip -cd cael341.gz | less


## Bash `IFS` Internal Field Separator

*	[`$IFS` - Linux Shell Scripting Tutorial](https://bash.cyberciti.biz/guide/$IFS)
*	[askubuntu: how-to-read-complete-line-in-for-loop-with-spaces](https://askubuntu.com/questions/344407/how-to-read-complete-line-in-for-loop-with-spaces)
*	[stackexchange: what-is-the-meaning-of-ifs-n-in-bash-scripting](https://unix.stackexchange.com/questions/184863/what-is-the-meaning-of-ifs-n-in-bash-scripting)


Mit `IFS` beeinflusst man in der *Bash* das Verhalten für die Wort-Separierung 
während der Zeichenketten- oder Datenstromverarbeitung. Der Standardwert für 
`IFS` ist die Zeichenfolge `<space><tab><newline>` (Leerzeichen, Tabulator,
Zeilenvorschub).

Beispiel 1:

```
$> for ITEM in $(ls -l); do echo "${ITEM}"; done

insgesamt
40
drwxr-xr-x
5
marco
marco
4096
Mai
16
(...)
```

Es wird jede Zeichenkette als Wort erkannt, 

Beispiel 2:

```
$> oldIFS=${IFS}; IFS=$'\n';for ITEM in $(ls -l); do echo "${ITEM}"; done;IFS=${oldIFS}

insgesamt 40
drwxr-xr-x  5 marco marco  4096 Mai 16 15:17 bin
drwxr-xr-x 22 marco marco 12288 Apr 27 19:22 Downloads
(...)
```


## Stoffsammlung

### Wann wurde das letzte Mal `apt update` ausgeführt?

```
$> ls -l /var/lib/apt/periodic

insgesamt 0
-rw-r--r-- 1 root root 0 30. Mär 06:46 autoclean-stamp
-rw-r--r-- 1 root root 0  3. Apr 00:07 download-upgradeable-stamp
-rw-r--r-- 1 root root 0  3. Apr 06:01 unattended-upgrades-stamp
-rw-r--r-- 1 root root 0  3. Apr 00:07 update-stamp
-rw-r--r-- 1 root root 0  3. Apr 06:01 upgrade-stamp
```

Über die Zeitstempel der Dateien (leere Dateien!) erfährst du, wann welche
Aktion durchgeführt wurde.

Die zuletzt aktualsierten Pakete erhätst du mit:

```
$> sudo less /var/log/apt/history.log
```

Die dazupassenden Terminal-Ausgaben der letzten Updates erhältst du mit:

```
$> sudo less /var/log/apt/term.log
```

### Wann wurde das System heruntergefahren?

```
$> last -x | grep shutdown
```


### systemd

*	[SUSE-Dokumentation: Der Daemon systemd](https://documentation.suse.com/de-de/sled/15-SP1/html/SLED-all/cha-systemd.html)
*	[ADMIN: systemd und seine Tools](https://www.admin-magazin.de/Das-Heft/2018/12/systemd-und-seine-Tools)
*	[ADMIN: Systemstart mit Systemd unter Linux](https://www.admin-magazin.de/Das-Heft/2014/10/Systemstart-mit-Systemd-unter-Linux)
*	[ADMIN: Ressourcenkontrolle mit Cgroups](https://www.admin-magazin.de/Das-Heft/2015/02/Ressourcenkontrolle-mit-Cgroups)
*	[ubuntu wiki systemd](https://wiki.ubuntuusers.de/systemd/)
*	[HEISE: Das Init-System Systemd, Teil 1](https://www.heise.de/ct/artikel/Das-Init-System-Systemd-Teil-1-1563259.html?seite=all)
*	[HEISE: Das Init-System Systemd, Teil 2](https://www.heise.de/ct/artikel/Das-Init-System-Systemd-Teil-2-1563461.html)
*	[www.freedesktop.org: systemd](https://www.freedesktop.org/wiki/Software/systemd/)
*	[systemd Tips And Tricks](https://www.freedesktop.org/wiki/Software/systemd/TipsAndTricks/)
*	[Homepage von Lennart Poettering](http://0pointer.de/blog/)
*	[systemd Status Update](http://0pointer.de/blog/projects/systemd-update-3.html)

```
$> systemctl list-units --type service --state=active

# Abhängigkeiten zwischen den einzelnen Units zeigt systemctl mit den 
# Optionen "list-dependencies --after" beziehungsweise "--before" an.
```

### how to check if script is getting sourced

```
#!/usr/bin/env bash

# --------------------------------------------
# check call context of script
#
#    $> ./script ........ script runs in a subshell
#    $> bash script ..... script runs in a subshell
#    $> . script ........ script is getting sourced --> does not work !!!
#    $> source script ... script is getting sourced
# --------------------------------------------

LAST_FIELD_FROM_LAST_COMMAND=$_
#echo "LAST_FIELD_FROM_LAST_COMMAND=${LAST_FIELD_FROM_LAST_COMMAND}"

SCRIP_NAME=${BASH_SOURCE[0]}
#echo "SCRIP_NAME=${SCRIP_NAME}"

BASH_CALL=$(which bash)
#echo "BASH_CALL=${BASH_CALL}"

SCRIPT_IS_SOURCED="FALSE"
if [ "${LAST_FIELD_FROM_LAST_COMMAND}" != "${BASH_CALL}" ]; then
    if [ "${LAST_FIELD_FROM_LAST_COMMAND}" != "${SCRIP_NAME}" ]; then
        SCRIPT_IS_SOURCED="YES"
    fi
fi

if [ "${SCRIPT_IS_SOURCED}" = "YES" ]; then
    echo "script is getting sourced"
    return 1
else
    echo "script runs in a subshell"
    exit 1
fi

exit 0
```
