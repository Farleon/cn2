# Reek C - IPv6 Adressering
## Bespreek de structuur en numerieke voorstelling van IPv6 adressen.
* Een IPv6 bericht bestaat uit 128 bits (16 bytes)
    * Biedt een groot adresbereik aan, zodat protocollen als NAT overbodig worden.
* Basisrepresentatie: P:Q:R:S:T:U:V:W
* Notatie:
    * Voorloopnullen zijn overbodig:
        * `FFE1:1:... == FFE1:0001:...` 
    * Opeenvolgende `[:]0000[:]` mogen vervangen worden door `::`
        * `FFE1::1EFF == FFE1:0000:0000:0000:0000:0000:0000:0000:1EFF`
        * Niet gespecifieerde adres `0:0:0:0:0:0:0`
        * Loopback adres = `::1`
    * Willekeurig 2 byte segment mag voorgesteld worden via dotted-decimal notatie:
        * `FE80::5EFE:192.168.41.30 == FE80::5EFE:C0A8:291E`
* Elk adres bestaat uit netwerkprefix en een interface id0
    * Enkel prefixlengte syntax kan gebruikt worden: `2001:410:1::/48`
* Nieuwe adresstructuur biedt mogelijkheden om zowel de efficientie van de adrestoewijzing als de performantie van het routingproces te optimaliseren.

## Geef en bespreek de verschillende types IPv6 adressen. Geef onder andere van elk van deze types de structuur, hun interpretatie, relevante voorbeelden en eventuele subtypes.

### Globale Unicast adressen
* Formaatprefix (eerste 3 bits) 001 -> alle unicast adressen `2000::/3`
* Nagenoeg ongewijzigd tov ipv4
	* Met elke interface moet minstens 1 unicast adres geassocieerd zijn
* Unicast adressen die mondiaal uniek moeten zijn, worden globale unicast genoemd
* IPv4 = netwerkprefix en interfaceId
* IPv6 kan nog verder opgesplitst worden in hierarchische niveaus
    * Top-Level aggregation Identifier (TLA)
        * 13 bit veld
        * hoogste niveau (toegewezen aan tier 1 isp's) 
        * Ten hoogste 8192 routes met prefixlengte /16 in default-free routes
    * Next-Level Aggregation Identifier (NLA)
        * 24 bit veld
        * op provider niveau toegewezen
        * Vormt samen met de TLA de publieke topologie van het internet (Eerste drie segmenten van de colon-hexadecimal notatie)
    * Site-Level Aggregation Identifier
        * 16 bit veld
        * Klant kan zo topologie afstemmen op structuur van zijn privaat netwerk
        * Met de beschikbare 16bits kan elke organisatie eigen interne netwerkstructuur creeeren mbv een hierarchie van subnetten op dezelfde manier als ipv4
    * Interface ID
        * 64 bit veld
        * Random bepaald
        * Kan ook eenduidig afgeleid worden uit het unieke MAC-adres (6 bytes) van de netwerkkaart
            * Omzetting op basis van IEEE EUI-64 formaat
	            * Eerste 3 bytes van MAC + FF:FE + Laatste 3 bytes van MAC
            * Tenslotte wordt laatste bit eerste byte gecomplementeerd (standaard 0 bij unicast MAC adres)

### Lokale Unicast adressen
* Dienen voor intern gebruik binnen een organisatie
* Worden niet doorgestuurd door routers buiten de organisatie
* 2 soorten:
    * Sitelokale adressen
        * Syntax: Formaatprefix FEC0:0:0/48
        * Vrij te kiezen SLA veld (maakt subnetting mogelijk)
        * Gebruikt voor verwerken verkeer, zonder dat versturen naar het internet mogelijk is
        * Te vergelijken met IPv4 private adresblokken 10/8, 172.16/12, 192.168/16
    * Linklokale adressen
        * Syntax: Formaatprefix FE80:0:0:0/64
        * Gebruikt om knooppunten op een enkele netwerkverbinding te nummeren
        * Laten geen subnetting toe
        * Te vergelijken met IPv4 169.254/16 APIPA adressen
        * Worden automatisch toegewezen aan elke interface
### Multicast adressen
* Relatief ongewijzigd t.o.v. IPv4
	* Mogen alleen als bestemmingadres gebruikt worden
* Netwerkinterface wordt geconfigureerd met een lijst MAC-Multicast adressen
    * Deze moeten doorgestuurd worden naar een hogere protocollaag
* Syntax: Formaatprefix (eerste segment in de colon-hexadecimal notatie): FFvs::
    * Vlag veld (v)
        * Bepaalt het type adres, is 4 bits lang en enkel de laagste bit is tot nu gedefinieerd
        * v = 0: Adres is een permanent adres, overige 7 bytes leggen speciale betekenis vast
        * v = 1: adres is transient (tijdelijk), kan adhoc gebruikt worden en ipv6 multicast adressen van de vorm FF1s::w.x.y.z worden automatisch gemapt op ethernet, multicast adressen van de vorm 33-33-w-x-y-z
    * Scope veld (s)
        * Geeft het bereik van het multicast adres aan
            * Globaal, site-lokaal, subnet, organisatie 
* Naast linklokaal unicast adres wordt ook een solicited-node multicast adres toegewezen (aan de interface)
    * Syntax: FF02::1:255x.y.z
    * x.y.z wordt bepaald door de laatste 3 bytes van het unicast IP adres
    * Zorgt er voor dat adresresolutie slechts een enkele klant verstoort en niet alle knooppunten van het netwerk

### Anycast adressen
* Kunnen gedeeld worden door meerdere knooppunten
    * Slechts ontvangen door 1 knooppunt
* Handig voor aanbieden van diensten
    * Aan elke dienst een apart anycast adres toekennen 
* Client software dient exacte IP van server niet te kennen, maar kan gewoon het anycast adres aanspreken om bij de juiste server terecht te komen
* Anycast adressen worden toegewezen uit de ruimte van unicast adressen en kunnen niet onderscheiden worden.
    * Interfaces moeten dus specifiek geconfigureerd worden om bepaald anycast adres te aanvaarden
    * Routers moeten op de hoogte zijn van de locatie van elk anycast adres
* De interfaces van een router worden steeds geconfigureerd met het subnet-router anycast adres
    * Vormen door het netwerkadres aan te vullen met een interface ID dat uit allemaal nullen bestaat
    * Zo is het eenvoudig mogelijk om op een subnet een willekeurige route te bereiken door het netwerk te adresseren.