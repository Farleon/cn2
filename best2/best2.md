# BesturingsSystemen

### Les Due
#### Commando's deel 1
#### head, tail
**head** - output the first part of files
**tail**- output the lastpart of files

	head [-n [-]#] [-c [-]#] ...
		-n	telt lijnen 
		-c	telt bytes
		# voorafgegaan door -: behalve # laatste	
	
	tail [-f|F] [-n [+]#] [-c [+]#] ...
		-f	blijft groeiend bestand bekijken
		-F	laat sluiten en openen bestand toe
		# voorafgegaan door +: vanaf lijn/byte #


#### nl, cut
**nl** - number lines of files
**cut** - remove sections from each line of files

	nl [-v#] [-ba] [-nrz] ...
		-v	initiëel lijnnummer (i.p.v. 1)
		-ba	nummert blanco lijnen ook
		-nrz	nummers voorafgegaan door 0-teken(s)
	
	cut [-c|f ] [-d ] [-s] [--complement] ...
		-c	selecteert kolomnummers
		-f	selecteert veldnummers
		-d	stelt velddelimiter in (i.p.v. TAB)
		-s	enkel lijnen waarin delimiter voorkomt
		--complement	complementeert selectie
#### tr
**tr** - translate or delete characters
	
	tr [-s] [-d] [-c] ... [...]
		-s	opeenvolgende exemplaren van hetzelfde
	        teken vervangen door één enkel teken
		-d	verwijdert i.p.v. vervangt
		-c	neemt complement van verzameling tekens


#### Voorbeelden  deel 1

	//geef eerste 4 lijnen bestand
	head -n 4 bestand 
	
	 //geef eerste lijnen behalve 4 laatste
	head -n -4 bestand
	
	//geef alles behalve laatste byte van bestand
	head -c - 1 bestand 
	
	 //Geeft laatste 4 lijnen
	tail -n 4 bestand
	
	 //geeft alles behalve 4 eerste lijnen
	tail -n +4 bestand
	
	//geef alle lijnen, maar lijn 3 2x en splitsen bij 3
	head -n 3 bestand
	tail -n +3 bestand 
	
	//geeft alle ljjnen in totaal, geen dubbels, splits bij totaal-3
	head -n -3 bestand
	tail -n 3 bestand 
	
	//Neem alles buiten de eerste 3s en daarvan de eerste 2
	tail -n +3 bestand | head -n 2 
	
	//toon inhoud, nummer lijnen, nummer ook lege lijnen (-ba)
	nl -ba bestand 
	
	//nummer volgens vast formaat (-nrz)
	nl  -ba -nrz bestand
	
	 //eerste 20 lijnen, nummer lijnen (niet whitespace), vervang kleine tekens door grote
	head n -20 bestand | nl | tr 'a-z' 'A-Z'
	
	//vervang aeiou door .
	head n -20 bestand | nl | tr 'aeiou' '.' 
	
	 // vervang a door f vervang e door s vervang al de rest door 5 
	head n -20 bestand | nl | tr 'aeiou' 'fs5'
	
	 //vervang tabs door spaties
	head n -20 bestand | nl | tr -s '\t\ ' ' aeiou' '.'
	
	//toon bestanden met info, toon enkel karakterkolom 2-9 (geen delimiter)
	ls -l | cut -c 2-9 
	
	//Ook al vraag je eerst kolom 3 en daarna kolom 1, toch zal er 1  3 getoond worden
	cut -d \; -f 3,1 < c 
	
	//Selecteert alles behalve kolom 2
	cut -d \; -f 2 --complement < c 
	
	//join bestand 1 kol 3 en 2 kol 2, toon enkel veld 1 van bestand 2
	join -t \; -1 3 -2 2 -o 2.1 
	
	//wat zijn de unieken, alfabetisch, hoeveel keer komen ze voor sort is nodig voor uniq, sorteer op aantal desc eerste 10
	join -t \; -1 3 -2 2 -o 2.1 w bestand | sort | uniq -c | sort -nr | head -10 

	//vervang alles wat geen cijfer is door een .
	tr -c '0-9' '.' 
	
	//verwijder alles wat geen cijfer is
	tr -dc '0-9' 

	//plak alles achter mekaar en echo
	ls -l 0? | xargs -t echo 
	
	//plak alles achter mekaar in groepjes van 3 en echo, vraag of ge de shit moet doen per groepjes
	ls -l 0? | xargs -n 3 -t -p echo 


#### Commando's deel 2
#### Grep, uniq
**grep, egrep, fgrep** - print lines matching a pattern. In addition, two variant programs egrep and fgrep are available.  Egrep is the same as grep -E.  Fgrep is the same as grep -F.

	grep [-E] [-f ...] [-e ...] [-i] [-x] [-r] [-v]
	     [-c] [-n] [-l|L] [-A|B|C #] ...
		-E	extended i.p.v. basic regexps
		-f	bestand met regexp(s) in elke lijn 
		-e	regexp(s) als argument
		-i	ongevoelig voor hoofdletters
		-x	alle regexps beschouwd tussen ^ en $ 
		-r	doorloopt map recursief
		-v	selecteert lijnen die niet voldoen
		-c	telt enkel aantal gevonden lijnen
		-n	vermeldt lijnnummers
		-l	bestanden waarin regexp wel voorkomt
		-L	bestanden waarin regexp niet voorkomt
		-A|B|C # 	extra contextlijnen na, voor 
	             of rond gevonden lijnen
**uniq** - remove duplicate lines from a sorted file

	uniq [-d] [-u] [-c] [-i] [-s#] ...
		-d	enkel lijnen die meermaals voorkomen
		-u	enkel lijnen die ééns voorkomen
		-c	berekent frequenties van elke lijn
		-i	ongevoelig voor hoofdletters
		-s#	negeert eerste # tekens van elke lijn



#### perl
**perl** - Practical Extraction and Report Language

	perl [-n] [-a] [-F ...] [-e '...'] ...
		-n	extra while (<>) {} rond perlcode,
	        met uitzondering van BEGIN {...} en     
	        END {...} blokken
		-a	autosplit invoerlijn in @F (cfr. awk)
		-F	bepaalt regexp voor autosplit
		-e	perlcode als argument i.p.v. in eerste 
	        bestand argumentenlijst



####Voorbeelden  deel 2

	//selecteer lijnen uit bestand die voldoen aan regexp
	grep -e BEL$ bestand 
	
	//meerder regexp
	grep -e BEL$ NED$ bestand 
	
	//voldoe aan regexp uit bestand2
	join -t \; -1 3 -2 2 -o 2.4 w bestand | grep -f bestand2 | nl 
	
	//voldoe niet aan regexp
	join -t \; -1 3 -2 2 -o 2.4 w bestand | grep -vf bestand2 | nl 
	
	 //tel de uniqs
	join -t \; -1 3 -2 2 -o 2.4 w bestand | grep -vf bestand2 | sort | uniq -c
	
	 //x -> voldoe aan volledige lijn
	join -t \; -1 3 -2 2 -o 2.4 w bestand | grep -xvf bestand2 | sort | uniq -c

	//selecteer de lijn achter answer SECTION
		dig 8.8.8.8 | grep -A1 ";; ANSWER SECTION" | grep -v ";; ANSWER SECTION" 
	
	//hoeveel keer komt a voor in bestanden
		grep -c a 0? 
	
	//in welke bestanden komt a voor  (0? kolom 0)
		grep -l a 0? 
		
	//in welke bestanden komt a niet voor
		grep -L a 0? 
		
	//voer perl script uit, -n while lus, iedere lijn overlopen, -e schrijf perl op command line
	cut -d \; -f4 < bestand | sort | uniq -c | perl -ne 'print $_' 
	
	//invoer perl script is array -a opgeslaan in $F, doe iets met array, print s op het einde va de while lus
	cut -d \; -f4 < bestand | sort | uniq -c | perl -ane 'print; $s+=$F[0]\n; END{printf "%7s\",$s}' 

	
####Commando's deel 3

#### chown,  chmod, chgrp
**chown** - change file owner and group
**chmod** - change file access permissions

	chown -R user ...
	chgrp -R group ...
	chmod -R [code] ...


####Voorbeelden  deel 3

	//stats van een bestand lezen -rwxr-x--- enz.
	ls -l bestand 
	stat bestand
	
	//groep kan wijzigen, niet meer executen en others kunnen lezen
	chmod g+w, g-x,o+r bestand 
	
	//werk met octale code (http://www.unixmantra.com/2013/04/unix-linux-file-permissions.html)
	chmod 740 
	
	//geef textueel mask
	umask -p 
	
	//geef numeriek mask
	umask -S
	
	//omzetten ofzo idk
	umask 0077


####Wat info over permissions

special attributes bestaan (4e triplet), vrij verwaarloosd.
The setuid/setguid permissions are used to tell the system to run an executable as the owner with the owner\'s permissions.
Be careful using setuid/setgid bits in permissions. If you incorrectly assign permissions to a file owned by root with the setuid/setgid bit set, then you can open your system to intrusion.
You can only assign the setuid/setgid bit by explicitly defining permissions. The character for the setuid/setguid bit is s.
So to set the setuid/setguid bit on file2.sh you would issue the command chmod g+s file2.sh.
Sticky Bit Special Permissions
The sticky bit can be very useful in shared environment because when it has been assigned to the permissions on a directory it sets it so only file owner can rename or delete the said file.
You can only assign the sticky bit by explicitly defining permissions. The character for the sticky bit is t.

####Commando's deel 4

#### ps, pgrep/pkill, pstree
ps - report a snapshot of the current processes.
pstree - display a tree of processes
pgrep -  look  up  processes based on name and other attributes
pkill - will send the specified signal (by default SIGTERM) to each process instead of listing them on stdout.

	ps [-e] [-f|F|l]
		-e	alle processen
		-f	belangrijkste procesattributen
		-F	meer processattributen
		-l	alternatief BSD formaat (inclusief procestoestanden)
	
	pstree [-p] [-n]
		-p	inclusief PID’s
		-n	numeriek gesorteerd op PID
	
	pgrep [-l|a] ...
		-l	inclusief procesnamen
		-a	inclusief volledige opdrachtlijn


####Voorbeelden  deel 4

	//info over processen
	ps -ef 
	
	//id van eigen proces
	echo $$ 
	
	//geef ids van alle bash processen
	pgrep bash 
	
	//ids van alle bash processen buiten jezelf
	pgrep bash | grep -v "$$" 
	
	//id + opdrachtinfo
	pgrep -a bash 
	
	//processen met hun processen die ze gestart hebben
	pstree | less 
	
	//testprog priemgetallen
	x=0; while((++x)) ; do  y = ($(factor $x)) ; ((${#y[@]}==2)) && echo $x; sleep 0.5; done
	
	//toon alle killable methodes
	kill -l 
	        //2 Sigint: wilde aub suiciden
	        //15 Sigterm: suicide nekeer agou
	        //9 sigkill: bam dood
	        //19 sigstop: bam stop
	        => volgorde 2-15-9
	        
	//kill optie procesid, je kan ook SIGKILL gebruik ipv 9
	kill 9 1700 
	
	//processen die op achtergrond overlopen (-l toon identificate)
	jobs -l
	
	//kill optie %jobNr
	kill 19 %1 
	
	//CTRL - Z proces wordt gestopt en gaat naar achtergrond
	//voorlaatste programma dat in achtergrond zit, + voor laatste
	fg %- 
	
	//komt overeen met CTRL - Z
	suspend -f 
	
	//uitvoerkanaal afsluiten
	1>&- 
	
	//invoerkanaal afsluiten
	0>&- 
	> s naar uivoer
	2 > /dev/null & iets van errors

