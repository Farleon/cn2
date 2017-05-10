# Reeks B - Configuratie van reverse DNS onder Linux
## Wat wordt beoogd met reverse DNS ? Hoe wordt dit gerealiseerd ? (§3.3.2)
### Wat
* Technologie die toelaat om domeinnaam te vinden voor gegeven IP
* Toegepast als beveiligingscontrole

### Hoe
* DNS PTR-type Records zorgen verantwoordelijk voor de omzetting.
* Elk IP heeft in-addr.arpa: `z.y.x.w.in-addr.arpa`, dit komt uit bij de machine die we zoeken.
* Om Reverse DNS toe te laten, moeten we primaire nameservers configureren in het zonebestand. Zie onderstaand voorbeeld.


	    $TTL 86400
	    @  IN  SOA  cauchy.analysis.fr.xix.  hostmaster.example.com. (
	           2001062501  ; serial
	           21600       ; refresh after 6 hours
	           3600        ; retry after 1 hour
	           604800      ; expire after 1 week
	           86400 )     ; minimum TTL of 1 day
	    ;
	    ;
		;  Zone NS records
		;
			@     NS	cauchy.analysis.fr.xix.
			cauchy.analysis.fr.xix. A	192.168.16.162
	    ;
		;  Zone records
		;
		162         IN            PTR	cauchy.analysis.fr.xix.
		200         IN            PTR	roberval.fr.xvii.
		201         IN            PTR	rolle.algebra.fr.xvii.
		202         IN            PTR	ruffini.it.xviii.
		203         IN            PTR	saccheri.it.xvii.


In this example, IP addresses 192.168.16.x (162,200,201,202,203) is pointed to the corresponding fully qualified domain name.
This zone file would be called into service with a zone statement in the /etc/named.conf file similar to the following:

**named.conf**

	zone "1.0.10.in-addr.arpa" IN {
	  type master;
	  file "example.com.rr.zone";
	  allow-update { none; };
	};


* Alle namen zijn relatief ten opzichte van zone
* 16.168.192.in-addr.arpa managed dus .z (16.168.192.z)