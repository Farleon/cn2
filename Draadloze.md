## 5. Draadloze Netwerken
### 5.1. Met welke opdrachten kan men op Windows toestellen nagaan welke de karakteristieken zijn van de eigen Wifi interface en van de Wifi zenders in de buurt. Geef aan welke karakteristieken vermeld worden.

|Opdracht|Uitleg|
|--------|------|
|`netsh wlan show networks`|Toont samenvatting gegevens omgeving.<br /><br />**Karakteristieken:**<br />- Naam, beschrijving, SSID<br />- Netwerktype<br />- Authenticatie<br />- Encryptie|
|`netsh wlan show networks mode=Bssid`|Toont gedetailleerde gegevens omgeving.<br /><br />**Karakteristieken:**<br />- Netwerktype, Authenticatie, Encryptie<br />- BSSID met: Signaal, Kanaal, Radiotype, Basis Snelheden en Andere Snelheden|
|`netsh wlan show interface`|Toont de eigen gegevens<br /><br />**Karakteristieken:**<br />- Naam, Beschrijving, GUID, Fysiek adres, Status, SSID, BSID<br />- Netwerktype, Radiotype, Authenticatie, Encryptie, Connectie<br />- Kanaal, Ontvangstsnelheid, Verzendsnelheid, Signaal<br />- Profiel, Hosted netwerk status|

#### Output netsh wlan sh networks
    SSID 1 : Geerinck Beneden
        Network type            : Infrastructure
        Authentication          : Open
        Encryption              : WEP

#### Output netsh wlan sh networks mode=Bssid
    SSID 1 : Geerinck Beneden
        Network type            : Infrastructure
        Authentication          : Open
        Encryption              : WEP
        BSSID 1                 : 5c:33:8e:59:75:1a
            Signal              : 58%
            Radio type          : 802.11g
            Channel             : 11
            Basic rates (Mbps)  : 1 2 5.5 11
            Other rates (Mbps)  : 6 9 12 18 24 36 48 54
         
#### Output netsh wlan sh int
        Name                    : Wi-Fi
        Description             : Qualcomm Atheros AR938x Wireless Network Adapter
        GUID                    : 3e423c56-0869-436c-b2ce-c7618f572880
        Physical address        : 90:f6:52:0a:0d:04
        State                   : connected
        SSID                    : Geerinck Beneden
        BSSID                   : 5c:33:8e:59:75:1a
        Network type            : Infrastructure
        Radio type              : 802.11g
        Authentication          : Open
        Cipher                  : WEP
        Connection mode         : Profile
        Channel                 : 11
        Receive rate (Mbps)     : 54
        Transmit rate (Mbps)    : 54
        Signal                  : 58%
        Profile                 : Geerinck Beneden
        Hosted network status   : Not available
<p style="page-break-after:always;"></p>
### 5.2. In welk opzicht zijn Ad Hoc routingprotocollen (in wireless meshes) anders dan de meer traditionele routingprotocollen (bedoeld voor internetwerken die uit bekabelde subnetwerken bestaan)?
* Eventuele mobiliteit van de routers
* Uitvallen en herstel van verbindingen meer frequent, met variabele kwaliteit van de link
* Bijkomende criteria voor performantie en metriek, zoals stabiliteit onder mobiele omstandigheden, energie verbruik, signaal/ruis verhouding, ...
* Strikt minimale processing (energieverbruik) en netwerkbelasting gewenst bij topologiewijzigingen
* Alleen gedistribueerde algoritmen aanvaardbaar.

### 5.3. Geef de 2 fundamenteel verschillende manieren om Ad Hoc routingprotocollen te realiseren, inclusief hun relatieve voor- en nadelen en hun optimaal toepassingsgebied.
|Routing Protocol|Beschrijving|Voorbeeld|Voordelen|Nadelen|
|----------------|------------|---------|---------|-------|
|Proactieve of table driven protocollen|Werken met routing tabel zoals RIP of OSPF. Er zijn dus routes voor alles actieve verbindingen, berekend op basis van een metriek.|Optimized Link State Routing (OLSR)|- +Routes onmiddelijk bruikbaar, ook bij sporadisch gebruik<br/>+Routes aangepast bij wijzigen linkkarakteristieken.|- -Grote netwerkbelasting|
|Reactieve of on-demand protocollen|Pas als we bericht versturen zoeken we een pad (enkel indien noodzakelijk), de discovered paths van routes worden gecached voor performantiewinst. |Ad Hoc On Demand Distance Vector Routing (AODV)|- +Minder netwerkverkeer<br/>+Werkt in grote situaties met duizenden knooppunten|- -Initiele vertraging voor gegevensoverdracht mogelijk is (ICMP Unreacable)|

<p style="page-break-after:always;"></p>
### 5.4. Bespreek een concreet voorbeeld van een implementatie die tot 1 van deze categorieen behoort, met vooral aandacht voor de verschillen met het traditionele routingprotocol (voor bekapelde internetwerken), waarvan het is afgeleid.
#### Optimized Link State Routing : OLSR
* Geinspireerd door ospf, hier was echter een probleem bij; de traditionele adjacencies overlappen.
* LSA's router enkel doorgestuurd door beperkt aantal buren (Multioint Relays - MPR's).
* Minder overhead (tov OSPF) door meer selectieve flooding
* Alle routes verwijzen naar een MPR als eerste hop naar de eindbestemming.
* MPR Criterium: Beperkte deelverzameling bidirectionele buren van router, zodanig dat alle 2-hop routers onmiddelijke buur zijn van 1 van de geselecteerde MPR's
* Elke router meldt periodiek (hello) zijn buren, zodat elke router uiteindelijk ook de volledige topologie tot op afstand 2 kent --> MPR selectie
* Selectieve flooding via MPR's leidt niet noodzakkelijk tot route met optimale metriek (mbt tot bandbreedte)

### 5.5. Bespreek een concreet voorbeeld van een implementatie die tot de andere categorie behoort, nu met een gedetailleerde beschrijving van hoe de routingtabellen door specifieke berichtuitwisselingen ingevuld worden.
#### Ad Hoc On Demand Distance Vector Routing : AODV
* Veronderstelt bidirectionele verbindingen
* TTL route verlengd door elk gebruik ervan
* Eenvoudig principe: 3 berichttypes volstaan voor basis implementatie
    1. Bron Broadcast Route Request (RREQ)
        * RREQ pakket wordt gebroadcast door de bron die data wenst te sturen maar over geen recente route naar doel beschikt (TTL niet 0)
        * RREQ verwijzend naar bron en beoogd doel + sequentie nummer
        * RREQ wordt eenmalig doorgestuurd naar intermediare routers. Dit wordt niet bevestigd, er is collision mogelijk.
        * De intermediaire routers construeren een Reverse Path naar de bron
    2. Unicast van Route Reply (RREP)
        * Reply als men RREQ heeft ontvangen door beoogde doel of door intermediaire route die wel recente route beschikt
        * Via geconstrueerd Reverse Path naar bron wordt reply teruggestuurd
        * Intermediaire routers en de bron kunnen hun routingtabellen naar het doel bijwerken
        * Hello bericht (detectie buren): RREP met ip.ttl = 1
    3. Route Error (RERR)
        * Als verbinding verbroken wordt terwijl een route actief is dan wordt er een RERR verstuurd, om te tonen dat deze unbereikbaar is.
        * Zorgen voor updating van routingtabellen oa. indien routers mobiel
        * Na ontvangst van de RERR kan men terug de route discovery in gang steken als dit gewenst is.
    