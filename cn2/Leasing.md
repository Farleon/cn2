# Reek C - DHCP leaseprocessen en relay-agents
## Geef een overzicht van de verschillende types DHCP berichten. Hoe wordt in het bericht het type aangeduid ?
We duiden het type aan met de optie 53

* DHCP-Discovery bericht
    * Broacast naar lokale subnet om servers te ontdekken
    * Optie 51 voor specifieke lease-tijd aan te vragen
    * Optie 55 bevat een geordende lijst van gewenste opties
    * IP adres van  de client wordt opgevuld met 0.0.0.0
* DHCP-Offer bericht
    * Reactie op DHCP discovery bericht
    * Biedt een client een IP-adreslease aan
    * Biedt ook de aangevraagde opties aan
	    * -> Server reserveert het adres voorlopig 
    * Optie 54 wordt ingesteld met de serveridentificatie (IP-adres)
    * Als client geen DHCP Offer ontvangt gaat hij even wachten (2,4,8,16, 5min) en opnieuw Discopery sturen
* DHCP-Request bericht
    * Bericht om de DHCP Offer te finaliseren
        * Dient dus om adreslease te vernieuwe/verlengen
    * Optie 54 wordt ingesteld met de serveridentificatie (IP-adres)
* DHCP-Decline bericht
    * Bericht van client naar server die initialisatieproces herstart indient foutieve configuratieinformatie ontvangen is.
* Positief DHCP-Acknowledgment bericht
    * Bevestigd de lease
    * Stuurt de vereiste DHCP-opties mee
    * DHCP servers wiens voorstel niet geaccepteerd werd stoppen de reservering van het aangeboden IP.
* Negatief DHCP-Acknowledgment bericht
    * Kan bij ongeldig of reeds toegekend IP-adres, of bij aanvraag subnet waarvoor hij geen DHCP scope ter bschikking heeft
    * Herbegin de volledige DHCP initialisatie procedure
* DHCP-Release bericht
    * Bericht van client om huidige lease vrij te geven
* DHCP-Inform bericht
    * Vraagt aanvullende opties aan de server
        * Zoals DHCP Discovery optie 55 

<p style="page-break-after:always;"></p>
## Bespreek in detail de opeenvolgende stappen van beide DHCP leaseprocessen.
* **Initialisatie**
    * Client broadcast een DHCP discovery bericht
        * Bronadres: 0.0.0.0
        * Dieladres: 255.255.255.255
        * Opties 51 en 55 ingesteld
        * Broadcast discovery (2,4,8,16,5min) tot hij DHCP Offer krijgt
    * Servers antwoorden met DHCP Offer
        * Server ID in optie 54
        * DHCP server reserveert het aangeboden adres
    * Client selecteert lease wanneer hij offer krijgt en vraagt deze formeel aan met een DHCP request
        * Gekozen server ID in optie 54
    * Server bevestigt lease met Positief DHCP Acknowledgment bericht
        * Broadcast bericht zodat andere servers tijdelijk gereserveerde leases kunnen vrijgeven.
        * Weigeren van lease met een negatieve DHCP Acknowledgment (indien de client een ongeldig/reeds toegekend adres aanvraagt)
    * Client vraagt eventuele extra informatie op via DHCP Inform bericht
    * Client neemt de lease in gebruik
        * kan deze ook weigeren met DHCP Decline wanneer de configuratie parameters fout zijn
    * Client geeft lease opnieuw vrij met behulp van een DHCP Release bericht.
* **Vernieuwing** (Gebeurt wanneer lease een tijdlang gebruikt is geweest (ongeveer na helft lease tijd expire time))
    * Client stuurt een DHCP request naar de server die de lease gestuurd heeft
    * Server antwoordt met een DHCP Acknowledgment bericht
        * Met opnieuw de opties ingesteld zodat de client zijn configuratie kan bijwerken
    * Indien geen DHCP Acknowledgment:
        * Client herhaald de stappen met wachttijd die oploopt in machten van 2: 2, 4, 8, 16, ...
        * Indien nog steeds geen Acknowledgment na ongeveer 7/8e van releasetijd, dan wordt de rebinding status geactiveerd (7/8 tenzij anders gespecifieerd in optie 59).
            * Client probeert lease te vernieuwen bij willekeurige DHCP Server door DHCP Request berichten te broadcasten.
            * Server antwoordt met een DHCP Acknowledgment.
        * Indien na 3 keer nog steeds geen Acknowledgment:
            * Client stopt met lease te gebruiken.
            * Herstart initialisatieproces.

<p style="page-break-after:always;"></p>
## Bespreek het doel en (in detail) de werking van DHCP relay-agents. Welke velden in DHCP berichten helpen deze functie realiseren ?
### Doel
* DHCP maakt gebruik van broadcast en zal dus enkel werken op een enkel subnet
    * Routers laten broadcastberichten niet door
* Een relay agent maakt het mogelijk om DHCP broadcastberichten door te sturen naar andere subnetten.

### Werking
 1. DHCP Client broadcast een DHCP Discovery
 2. Relay agent contrleert het veld met het gateway IP adres in de header. Als dit IP adres 0.0.0.0 bevat vult hij z'n eigen adres in en stuurt het naar het volgende subnet, waar de DHCP server zich bevindt
 3. DHCP server ontvangt Discovery bericht en indien de server over meedere scopes bezit bepaalt het gateway adres uit welke scope een adres gekozen moet worden. Indien er geen scope is voor het aangevraagde subnet kan geen adres gegeven worden. Er wordt een Offer gestuurd naar de relay.
 4. De relay geeft het Offer door aan de client, dit door broadcasting aangezien de client nog geen adres heeft.
Request en Acknoledgement werken volgens zelfde methode

### Vereiste velden
* Hop veld
    * Wordt met 1 verhoogd telken een relay agent het bericht opnieuw broadcast. 
* Gateway veld
    * hierin wordt de IP van de doorsturende router opgeslagen
    * Dit wordt ook gebruikt om de scope te bepalen van adressen bij keuze van een lease.

## Beschrijf wat er gebeurt op DHCP niveau bij het heropstarten van een Windows Server of een Linux toestel, nadat men een shutdown heeft uitgevoerd.
* Zowel Windows als Linux loggen hun DHCP leases
    * Linux: file /var/lib/dhcp/dhclient.leases
    * Windows: hive register`
Na opnieuw opstaren DHCP client wordt onmiddelijk gepoogd om de oude lease te verlengen door DHCP request te versturen. Als de server onbereikbaar is zal de lcient zijn default gateway pingen en als die bereikbaar is zullen ze stilzwijgend de lease gebruiken die hen toegekend was.

<p style="page-break-after:always;"></p>
## Beschrijf wat er gebeurt indien een Windows Server of Linux DHCP-cliënt in eerste instantie geen DHCP-server kan bereiken.
* Linux en Windows DHCP clients kunnen zichzelf configureren om met een tijdelijke IP-configuratie te werken
* = Automatic Private IP Addressing (APIPA) (of automatische IP Configuratie)
* Toegewezen uit gereserveerde klasse B bereik 169.254/16
    * Dit bereik wordt niet op het internet gebruikt
* De DHCP client voert een conflictdetectie uit met een Gratuitous ARP aanvraag
    * Indien duplicaat IP adres wordt een nieuw random IP adres gekozen in dit bereik
* Na toewijzing van het tijdelijke adres blijven de clients in de achtergrond op periodieke intervallen contact zoeken met een DHCP Server
    * Indien toch DHCP Server gevonden, wordt een lease aangevraagd en vervalt het tijdelijke adres. 
