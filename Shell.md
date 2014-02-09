SHELL
=====

Add timestamp to history command: `export HISTTIMEFORMAT='%F %T '`

Bash shortcuts
--------------

ctl-a: go to begining of line
ctl-e: go to end of line
alt-b: move word by word backward
alt-f: move word by word forward
ctl-K: cut characters at right of cursor
ctl-U: cut characters at left of cursor
ctl-L: clear terminal
ctl-N: cut the full line
ctl-W: cut characters at right of cursor (until next space)
ctl-Y: past cut characters
ctl-t: inverse the 2 words before cursor
alt-t: inverse the 2 words after cursor

Arguments substitution
----------------------

Reuse arg from last command: `!!:N` where N is args number

Parameters replacement
----------------------

myvar=hello.txt

`${myvar:-world}`: if myvar is undefined, return "world", else return "hello.txt"

`${myvar:=world}`: if myvar is undefined or null, return "world" and replace actual value, else return "hello.txt"

`${myvar:?world}`: if myvar is undefined or null, exit shell with RC≠0 and print "world" on stderr

`${myvar:+world}`: if myvar is defined, return "world", else return nothing

`${#myvar}`: return characters length

`${parameter%.txt}`: return myvar value without characters after "%" (ex: "hello")

`${parameter#hel}`: return myvar value without prefix after "#" (ex: "lo.txt")

`${myvar/hello/goodbye}`: replace first string by second in myvar value and return it (ex: "goodbye.txt")

`${myvar/ll/tt/g}`: replace every occurence of first string by second in myvar value (ex: "hetto.txt")

`${myvar:2}`: return value following specific position (ex: "llo.txt")

`${myvar:2:3}`: return value following specific position with length specified in 3rd argument (ex: "llo")

Dynamic variables
-----------------

```shell
myvar1=\$myvar2
echo $myvar1 # nothing
eval echo $myvar1 # nothing
myvar2=hello

eval echo $myvar1 # hello
```

Differences between break and continue
--------------------------------------

```shell
for a in a b c; do
  for b in c b a; do
    if [ $a == $b ]; then
      continue
    fi
    echo -n "$a$b "
  done
done # ac ab bc ba cb ca


for a in a b c; do
  for b in c b a; do
    if [ $a == $b ]; then
      break
    fi
    echo -n "$a$b "
  done
done # ac ab bc

for a in a b c; do
  for b in c b a; do
    if [ $a == $b ]; then
      break 2
    fi
    echo -n "$a$b "
  done
done # ac ab
```

Sed
---

Replace second occurence of word for each line in file: `sed 's/Linux/AIX/2' file.txt`

Replace regexp (case insensitive): `sed 's/Linux/AIX/i' file.txt`

Replace regexp in line matching "SYSTEM": `sed 'SYSTEMs/Linux/AIX/' file.txt`

Replace third last characters of lines: `sed 's/...$//' file.txt`

Remove comments in file: `sed -e 's/#.*//;/^$/d' file.txt`

Remove HTML tags in file: `sed -e 's/<[^>]*>//g' file.txt`

Awk
---

Search lines matching 1 of 2 expressions: `awk '/expr1/ || /expr2/' file.txt`

Display lines number matching "KO" in 2nd column: `awk 'BEGIN { count=0 } $2 ~/KO/ {count++; } END { print count; }' file.txt`

Divers
------

Interdire un fichier en modification et suppression (meme en tant que root) chattr +i file
Supprimer l'interdiction de modification et suppression: chattr -i file

Formater un fichier en colonnes: column -t file

Trier un fichier sur la 2ème colonne et supprimer les doublons: sort -u -t: -k 2 file

Afficher les differences entre 2 fichiers en omettant les lignes vides: digg -w file1 file2

Supprimer un fichier commençant par un caractère exotique: ex fichier nommé "*"
touch *
ls -i # 30762 *
find . -inum 30762 -exec rm {} \;

Debugger un script: sh -n (vérifie le script sans l'exécuter)
Tracer les valeurs successives d'une variable dans un script: trap 'echo VARIABLE myvar> \"$myvar\""' DEBUG

Comparer 2 répertoires: diff -rq /home/user1/ /home/user2/


Supprimer les lignes ne commencant pas par une pattern: sed -i '/^asmcmd/!d' tmp.lst

Supprimer les lignes 1 et 3 dans un fichier: sed -i '1d;3d' tmp.txt


Remplacer les espaces par des tabulations dans un fichier: sed -i 's/ * /\t/g' tmp.txt

Relancer la dernière commande en remplacant une pattern:
- ^less^vim (remplace less par vim dans la derniere commande)


Convertir un fichier en minuscules:
- dd if=input.txt of=output.txt conv=lcase
- tr '[:upper:]' '[:lower:]' < input.txt > output.txt
- awk '{ print tolower($0) }' input.txt > output.txt
- perl -pe '$_= lc($_)' input.txt > output.txt
- sed -e 's/\(.*\)/\L\1/' input.txt > output.txt



Handle exit code:
- (hostname; exit 4) && echo OK || echo KO
- (hostname; exit 0) && echo OK || echo KO

Grep multiples patterns:
- grep  -v "^#\|^'\|^\/\/" comments
- awk '/pattern 1/&&/pattern 2/ {print}' /home/file
- awk '/pattern 1/||/pattern 2/ {print}' /home/file

Grep OR
- grep 'pattern1\|pattern2' filename
- grep -E 'pattern1|pattern2' filename
- egrep 'pattern1|pattern2' filename
- grep -e pattern1 -e pattern2 filename

Grep AND
- grep -E 'pattern1.*pattern2' filename
- grep -E 'pattern1.*pattern2|pattern2.*pattern1' filename


Character Class Expressions:
- [:digit:]      Only the digits 0 to 9
- [:alnum:]      Any alphanumeric character 0 to 9 OR A to Z or a to z.
- [:alpha:]      Any alpha character A to Z or a to z.
- [:blank:]      Space and TAB characters only.

Grep whole word:
- grep -i "\bthe\b" comments

Génération de password avec /dev/urandom
# alias gen_pw='< /dev/urandom tr -dc _A-Z-a-z-0-9 | head -c8'
# gen_pw
wbk0ewpn

Renommage de fichiers
- txtfile=azerty.txt; mv $txtfile ${txtfile%.*}.log
- for file in `find . -name '*%20*'`; do newfile=`echo $file | sed -e "s/%20/_/g"`; mv $file $newfile; done

Variables shell
- !$ (rappel dernier param)
- variable="Hello World"; echo "${variable:6}" (World)
- variable="Hello World"; echo "${variable:0:5}" (Hello)
- ${#variable} (print the length of $string)
- ${variable/substring/replacement} (replace first match of $substring with $replacement)
- ${variable//substring/replacement} (replace all matches of $substring with $replacement )






( <command> 2>&1 | tee compile.log && exit $PIPESTATUS )
