# Reeks B - Configuratie van DNS servers onder Linux Deel 3
De figuur in bijlage stelt een intranet bestaand uit een aantal Linux ...(cfr.vraag B2)... achterhalen. Geen enkele zone heeft een secundaire nameserver. Je hoeft geen reverse DNS te configureren.

## Bespreek in detail het formaat van een configuratiebestand. Je mag dit doen op basis van één van de oplossingen in a), doch je moet ook alternatieve opdrachten en sleutelwoorden beschrijven, die je niet noodzakelijk hebt gebruikt. (§3.3.3)

    options {
    	directory "/var/named";
        forwarders { ip-addressen };
    };
    
    zone "oratoria.XVII.it" IN {
    	type master;
    	file "oratoria.XVII.it.zone";
    	allow-update { none; };
    };
    
    zone "XIX.it" IN {
    	type master;
    	file "XIX.it.zone";
    	allow-update { none; };
    };
    
    zone "XX.us" IN {
    	type master;
    	file "XX.us.zone";
    	allow-update { none; };
    };
    
    zone "iii.hogent.be" {
        type slave;
        file "iii.hogent.be";
        masters { 192.168.16.16; };
    };

* Named gebruikt named.conf (/etc/named.conf)
* Bevat basisinstellingen voor de serverdaemon te configureren
* Vermeldt ook de namen van de zonebestanden
* Meest belangrijke keywords zijn zone en options (soort JSON structuur)
* options
    * directory padnaam 
        * geeft dir waar zonebestanden zich bevinden
    * forwarders { ip-adressen; }; 
        * Als geen DNS info gevonden, vraag aan deze IP's
        * Als ook deze geen terugvinden, vraag aan root server
    * allow-tranfer{ ip-adressen; }; 
        * IP's van computers die zone transfers kunnen uitvoeren (noodzakelijk voor secundaire nameservers en voor ls opdracht in nslookup)
        * Indien deze lijn ontbreekt kan elke computer een zone transfereren.
    * blackhole{ ip-adressen; }; 
	    * Specifies which hosts are banned from the server. This option should be used when particular host or network floods the server with requests. Default is none;
* zone
	* allow-query
		* Specifies the clients that are allowed to request information about this zone. Setting of this option overrides global allow-query option. The default is to allow all query requests.
	* allow-transfer { ip-adressen; }; 
		* Specifies the slave servers that are allowed to request a transfer of the zone's information. The default is to allow all transfer requests.
	* allow-update
		* Specifies the hosts that are allowed to dynamically update information in their zone. The default is to deny all dynamic update requests.
    Be careful when allowing hosts to update information about their zone. Do not set IP addresses in this option unless the server is in the trusted network. Use TSIG key instead .
    * file
        * Specifies the name of the file in the named working directory that contains the zone's configuration data.
    * type
        * forward — Forwards all requests for information about this zone to other nameservers, waarvan de IP adressen vermeld zijn in de forwarders lijn bij options (globaal) of bij zone (lokaal)
        * hint — Het bestand dat hier opgegegeen wordt bevat een lijst van rootservers die bij het opstarten door de nameserver worden gebruikt. Enkel geraadpleegd om de namen van de echte rootservers terug te vinden.
        gebruikt zonenaam ".",  gebruikte bestand bevat lijst van rootservers.
        vb: `.    3600000 IN NS A.ROOT-SERVERS.NET.`
        * master — Designates the nameserver as authoritative for this zone. A zone should be set as the master if the zone's configuration files reside on the system.
        * slave — Designates the nameserver as a slave server for this zone. Master server is specified in masters directive.
    * masters { ip-adressen; }; 
        * Specifies the IP addresses from which to request authoritative zone information and is used only if the zone is defined as type slave.


## Stel het configuratiebestand en alle zonebestanden op van volgende DNS servers: ... . Gebruik relatieve DNS namen waar mogelijk. Gebruik noch forwarders, noch de $ORIGIN opdracht !

+- Zelfde als B1