Nestihl jsem projít. Otázky mohou být:
- Explain function of Border Gateway Protocol. What Are Different Bgp message types and their meanings. Explain Bgp path attributes and their influence on best route selection.
- What are Benefits of using MPLS? Describe Push, Swap, Pop operations in Mpls network and also explain Penultimate Hop Popping. Describe MPLS network architecture for virtual private networks. What is the difference between RD and RT. How MPLS labels are discovered and assigned in the MPLS networks.
- Describe a role of IP multicast. What Are The Protocols Used In IP Multicast. Explain a role of IGMP in IP multicast networks. Explain two versions of multicast tree and the difference between PIM sparse and dense modes.
- How does Ipv6 solve the problem of Ipv4 address exhaustion? Explain format of IPv6 address and its parts. Explain a function of stateless IPv6 address autoconfiguration (SLAAC) and protocol for discovering network neighbours.

## TCP protokol
- Přenos zpráv může být *simplex* nebo *duplex*, model uvažuje obousměrný (duplex)
- Procesy: *Vzdálené* nebo *Lokální*
	- z hlediska komunikace mezi procesy na typu nezáleží
	- **soklokal** = identifikátor lokálních procesů
	- proces je vzdálený, pokud si s jiným posílá informace v rámci LAN, MAN nebo WAN
- Modely:
	- **Client-Server** - asymetrická komunikace (FTP, WEB, ...), clienti typicky nemají paměť
	- **Peer2Peer** - rovnocenný přenos (Skype, BitTorrent, ...)
- řízení toku (flow control)
- řízení přetížení (congestion control)
- metoda klouzavého okna

### Pojem a význam soketů
- Identifikují začátek a konec procesů spojení
- Poskytují API procesům aplikace

### Posloupnost volání soketových funkcí (metod) pro klienta a server(TCP)
- Socket() - vytvoří socket a vrátí jeho číslo
- Bind() - zapne poslouchání socketu na adrese a portu
- Connect() - zapne spojení přes socket na adresu a port
- Accept() - přijme na socketu a vrátí nové číslo socketu
- Listen() - čekání na příjem na socketu
- Write(), Sendto() - zápis
- Recv(), ResvFrom() - čtení
- Close() - konec na socketu

### Struktura a význam záhlaví TCP datové jednotky (segmentu)
- -> rozdělení dat do bloků
- *ACK* - packet potrvzující příjem

### Fáze navázaní spojení
- Navazuje klient, server čeká
- **Handle** = identifikátor zásuvky pro proces v OS; nemohou existovat 2 stejní
1. SYN(seq=a) ->
2. SYN,ACK(seq=b, ack=a+1) <-
3. ACK(seq=a+1, ack=b+1) ->

### Fáze přenosu dat
- segmenty se příjímají do bufferu, kde se sestavují ve správném pořadí 

### Fáze ukončení spojení
1. FIN ->
2. ACK <-
3. FIN <-
4. ACK -> 

### Význam sekvenčních a potvrzovacích čísel
- **Sequence Number** = pořadové číslo vyslaného segmentu
- **Acknoledgement Number** = potvrzovací číslo, myslím, že se posílá poslední číslo souvisle přijatého segmentu +1 

### RTO a RTT, systém potvrzování
- **RTO** = Retransmition TimeOut, pokud ACK nepřijde do RTO, pošle se nepotvrzený znovu
    - pokud vyprší, host zdvojnásobí RTO do max. 60 s
- **RTT** = Round Trip Delay, čas odeslání a potvrzení segment

### Princip kumulativního potvrzování
- *Delayed Ack* - po max. 2. přijatém segmentu pošle ACK s číslem nejspodnější mezery bufferu
- *Cumulative Ack* - při přijmu segmentu navíc čeká max. 500 ms na dalsí segment a pak pošle potrvzení pro oba

### Princip TCP okna a jeho použití pro řízení TCP toku
- řídí velikost toku dat
- příjímač nastaví velikost okna pro vysílač

### Odhad rychlosti přenosu dat TCP spojení
- `propustnost = 8*WS/RTT [bit/s]`, `WS` je velikost okna vysílače
- RTT není konstantní, a tak se RTO v jeho závislosti také mění:
	- `RTO_est = a * RTO_est + (1-a) * RTT_current`
	- `a` in `[0.8, 0.9]`
- `RTO = max(1s, RTO_est + 4*RTT_Dev)`
	- `RTT_Dev = (1-b) * RTT_Dev + b * |RTT_curr - RTO_est|`

### Metoda řízení přetížení
Kritéria:
- *Efficiency* - co nejblíže pracovnímu bodu
- *Fairness* - co nejekvivalentnější sdílení bandwith
- *Distributedness* - znalost stavu celého systému
- *Convergence* - systém se dostane do optimálního stavu z každého startovního bodu

#### AIMD, MIAD, AIAD, MIMD
AIMD = additive increase/multiplicative decrease - optimální
MIAD = multiplicative incerase/aditive decrease - optimální
AIAD = additive increase/additive decrease - nekonverguje
MIMD = multiplicative increase/multiplicative decrease - nekonverguje

### Fáze pomalého startu (slow start) u TCP
- Motivace: Obdélník odrovná síť a osciluje.
- Ze 4 segmentů exponenciálně posílá víc, dokud nedostane *TD* (příjem 3 dupl. ACK)

### Fáze vyhýbání se přetížení (congestion avoidance)
- Pomocí TD a lineárním zvyšováním okna o +1 se drží cca 100% zátěže, dokud opět TD

### „New RENO“ princip pro vyhýbání se přetížení v síti
- Po 3 TD nastane TO a jde od začátku
#### Co se děje při události „triple duplicate“
TD = měkké přetížení, sníží zátěž o ssthresh/multiplicative decrease
#### Co se děje při události „RTO expiration“
- Tvrdé přetížení
- Začne znovu pomalý start
- Všechny rámce bývají zahozené, musí se posílat znovu
#### Metoda „fast retransmit“
**Fast Retransmit** - pokud se přijou 3x ACK, vyšle se chybějící segment znovu bez vypršení RTO

### Rozdíl mezi RENO a Tahoe
- U TD není nutné jít hodně dolů o ssthresh (pila)
- Ztrácení kapacity zamezí kubickou konvexní -> konkávní funkcí


## MPLS
- Motivace:
    - vyhodnocují se pouze cílové adresy
    - nerovnoměrná zátěž, využívá se vždy jen nejkratší cesta
- Princip vyhodnocování značek - *labels*
- Podporuje více protokolů
- V OSI - spojová a síťová vrstva (L2.5)

### Význam polí u MPSL značky
- 32-bit pole se značkou a dodatečným info
- *label stacking* - zřetězování více MPLS značek
- frame-mode -> vložení MPLS mezi L2 a L3; cell mode -> ATM (Async Transfer Mode)  záhlaví je přímo značkou MPLS
- `[. . . LABEL 20bit . . .][EXP 3bit][S][. TTL 8bit .]`
    - S = dno zásobníku

### Způsoby práce se značkami v MPLS síti (push, pop, swap, aggregate, untagged)
- **POP** - odstraní top label + pošle dál zbytek
- **SWAP** - zamění top label za jinou z databáze LFIB
- **PUSH** - zamění nebo vytvoří top label
- **AGREGATE** - pokud je top label poslední, odstraní ho a provede místní IP lookup, jinak je MPLS zahozen
- **UNTAG** - pokud je top label poslední, odstraní ho a pošle zbytek na next_hop, jinak je MPLS zahozen 

### Řetězení MPLS značek (několik značek za sebou) a jeho smysl
- Vytvoření cesty zásobníkem
- LSR (core router) používá pouze horní top label
- Hraniční LSR primárně značkuje vstupující IP pakety a posílá je do MPLS domény a naopak, přijímá z MPLS domény značkované pakety, odebere značku a posílá je do další části klasické IP sítě

### Pro jaké síťové služby lze použít MPLS sítě
- IP adresace, VPN
- Může to využít jakýkoli router podporující MPLS; navíc pokud routuje jen pomocí labelů, je to LSR (Label Switch Router)
- Obecně použitelné pro distribuované systémy

### Umístění MPLS hlavičky v L2 rámcích (Ethernet, PPP, FR, ATM, apod.)
- Mezi L2 a L3, např. `Eth | Label | IP | Data`

### Význam databází RIB, FIB, LIB, LFIB
- **RIB** (Routing Information Base) = routovací tabulka -> statické cesty, protokoly, připojení `show ip route`
- **FIB** (Forwarding Information Base) = tabulka sousednosti -> prefix + next_hop `show ip cef`
- **LIB** (Label Information Base) = tabulka značek -> label/prefix mapping `show mpls ldp bindings`
- **LFIB** (Label Forwarding Information Base) = tabulka nejlepších cest `show mpls forwarding-table` 

### Distribuce značek v síti
- *Nezávislá* ~ *Downstream unsolicited* = LSR může oznamovat label na IP síť sousedům kdykoli je to potřeba
- *Řazená* ~ *Donwstream on demand* = LSR oznamuje IP mapping na label pokud má informaci o labelu od níže položeného routeru nebo je koncovým routerem (egress)
#### Funkce LDP protokolu
- LSP = Label Switched Path, jednosměrná
- LDP = Label Distribution Protocol
- FEC = Ekvivalentní přepínací třída, množina datových jednotek putujících po stejné LSP
	- Např. FEC1 má přiřazeny packety s source X, dest Y, TCP
	- První položka, kterou routery řeší
- Motivace: Přiřazení značky má lokální význam a je potřeba informovat sousedy
- Co to dělá:
	- oznamuje značky na jednu FEC (tj. přes značku X je dostupná síť IP Y)
	- definuje mechanismus nalezení LDP sousedů (základní nebo rozšířená metoda)
- Fáze:
	1. Nalezení sousedů - multicastem hledání a čekání na odpovědi	
	2. Vytvoření spolehlivého kanálu mezi sousedy - TCP
- Transport address -> udává adresu, z níchž má vzniknout TCP spoj
- TCP se navazuje z větší na nižší transportní adresu (nemá reflektor jako v BGP, každý se musí spojit)

### Základní konfigurace MPLS sítě pro distribuci IPv4 prefixů
??? BGP + FEC? 
 
### Co je to PHP u MPLS
- PHP = Penultimate Hop Popping
- Motivace: V hraničním LSR se odstraní záznam v LFIB + hledá se next_hop v FIB => 2 prohledávání
- Řešení PHP: Značka v LFIB se odstraní v LER před LSR

### Distribuce značek pro VPNv4 sítě v MPLS
 
### Funkce VPNv4 v MPLS
- síť na sítí -> zjednodušuje, ale je to komplexní
- MPLS nerealizuje šifrování
- **Site** = místa, které se přes VPN propojují
- P-Network = provider network
- C-Network = customer network
- CE Router = hraniční uživatelský router v C-Network
- PE Router = hraniční router poskytovatele spojující obvykle více CE routerů
#### Význam a smysl RT (route target)
- 64bit identifikátor určující routery, co mají společně příjímat danou VPN
- Může exportovat nebo importovat přes BGP route targety z různých sites
#### Význam a smysl RD (route distinquisher)
- Atributy každého VPN směru jednoznačně identifikující IP-VPN prefixy (unifikují sítě) mezi sebou
- často je stejný jako route target
- Existuje pro oddělení sítí se stejnými IP
#### Význam a počet značek pro VPNv4 pakety v MPLS síti
- BGP packet pro update má navíc toto:                                 |
	- EXTENDED_COMMUNITIES - šíří se v ní route target, spíše info pro V
	- MP_REACH_NLRI - obsahuje RD + IPv4, stack (odpovídá pozici ve stacku RD + IP)
- \# značek ??? Jako počet hran ve VPN?
#### Distribuce značek VPNv4
- MP-BGP přenáší přes mesh connectivitu (každý PE s každým) info o konektivitě přes všechny PE routery
- **VRF** = VPN Routing & Forwarding Instance, virtuální směrovací tabulka pro danou VPN
	- Z jednoho routeru může uspořádat více virtuálních routerů
- PE routery musí být všechny propojeny, nebo BGP reflektor
- Komunikace formou hvězdy?


## Protokol BGP

### Funkce BGP
- vektorový vnější směrovací protokol
- možnosti volby atributů od administrátora => důvěra routerů
- slouží k propojení AS
- flexibilita a snadné propojení mezi routery (zejm. předávání routeovacích záznamů)
- garantovaný datový spoj (NLRI)
- aplikační TCP vrstva

### eBGP a iBGP relace a jejich smysl
- Typy:
	- eBGP -> komunikace mezi AS
	- iBGP -> komunikace uvnitř AS
- **Peer** = dvojice routerů, které mezi sebou komunikují
- **Neighbours** = sousedé v síti
- **Speaker** = každý router, ve kterém běží proces BGP
- administrace se provádí na základě důvěry v směrovací politiku
- V případě, že existuje více tras ke stejnému cíli, BGP nepošle sousedům všechny tyto trasy. Místo toho BGP
vybere pouze tu nejlepší trasu a pošle ji sousedům.

### Specifika iBGP (RR – router reflektor)
- roznáší dalším iBGP sousedům info o sousedech
- **Reflektor** - explicitně přeposílá vybraným sousedům informace o jiných sousedech

### Popis konečného automatu (FSM) BGP protokolu, jaký protokol a port používá BGP
1. *Connect* - při připojování ještě bez TCP; dočasný
2. *Active* - při připojování, skáče mezi Active; dočasný
3. *OpenSentMessage* - pošle info; dočasný, výměna
4. *OpenConfirm* -  posla info a čeká na odpověď; dočasný, výměna
5. *Established* - konečný stav; trvalý 
6. *Idle* - ?; trvalý
- Zprávy:
	- *OPEN* - Navázání spojení
	- **UPDATE** - info o změnách aktivních a zrušených směrech
	- *KEEP_ALIVE* - heartbeat
	- *NOTIFICATION* - ...
	- *ROUTE_REFRESH* - pokud se mění politika, pošle soused své směry, aby se nemuselo resetovat spojení

### Koncept sítě založené na propojení autonomních systémů
- **AS** = autonomní systém, sada routerů v rámci jedné administrace
- vnější svět vnímá AS jako jeden celek
- číslo AS je v 1 - 65535, ale 64512 - 65535 je pro soukromé účely, spravuje IANA

### NLRI (Network Layer Reachability Information)
- přenáší UPDATE zprávy se změnami směrovacích informací
- pouze jedna NLRI je v UPDATE, přestože tu může být více AS-PATH 

### Atributy BGP záznamů, priority (nejvyšší > nejnižší):
- **Next_hop** - povinný; pokud je nedostupný, končí spojení
- **Weight** - čím větší, tím preferovanější
- **Local_preference** - známý & diskrétní; pokud stejné, upřednostní trasu vytvořenou BGP na aktuálním routeru
- **AS_path** - povinný
- **MED** - známý & diskrétní; na rozdíl od předchozích ovlivňuje způsob vstupu do sítě
- **Community** - volitelný & tranzitivní


## IPv4 multicast

### Smysl použití multicastu a co to vlastně je
- Motivace: Umožnit posílání dat více příjímačům současně
- Možnosti:
	- Zdroj zreplikuje data pro všechny (overload odesílatele)
	- Síť se postará o replikaci příjímačům (= multicast)
- Výhody:
	- Menší zatížení zdroje
	- Efektivní využití přenosové kapacity sítě
	- Zdroje dat nepotřebují znát IP adresy všech přijímačů, jen jednu
IP adresu multicastové skupiny (skupiny se mohou dynamicky měnit) 
- Nevýhody:
	- Není garance doručení packetů
	- Při přetížení nesnižuje rychlost replikace
	- Mohou být doručené duplikované packety
	- Packety mohou přicházet přeházené

### Distribuční strom multicastu (sdílený a nejkratší strom)
- **PIM** = Protocol Independant Multicast - nalezení optimálních cest
- **IGMP** = Internet Group Management Protocol-  registrační protokol k zapojení do skupiny

### Význam IPv4 multicastové adresy
#### Lokální multicast rozsah
- LAN má vlastní multicast na L2, ale IP multicast je na L3
- L3 multicast je univerzálnější, než L2
#### Multicast adresa pro zprávy určené všem lokálním směrovačům
- Používají IP adresy třídy D
- Mají TTL = 1
- `224.0.0.0 -- 224.0.0.225`
#### Multicast adresa pro zprávy určené všem koncovým zařízením
- Šíří data internetem
- `224.0.1.0 -- 238.255.255.255`
#### Administrativní, lokální rozsah IPv4 mul. adres – „scope“
- Motivace: Omezení není flexibilní používáním pouze TTL
- Multicast omezují hraniční routery kombinací CIDR (např. site /16, org /14)
#### Vztah mezi mul. IPv4 adresou a mult. MAC adresou
- IP adresa má první 4 b fixní: 1110
- Deering ze Standfordu:
	- MAC se skládá z půlky od výrobce a z půlky adresy zařízení
	- Chtěl koupit 16 bloků MAC adres, ale supervisor mu dal peníze jen na jeden 0x01005E, a k tomu navíc si supervisor zablokoval poslední bit s 1, Deering má pevnou 0
- Z IP na MAC se mapuje posledních 23 bitů na posledních 23 bitů MAC
- Není jednoznačný -> existují IP adresy, které se mapují na stejnou MAC adresu

### Funkce a smysl použití IGMP protokolu
- Smysl: Informovat routery, že některá zařízení mají zájem o připojení
#### Verze IGMP (v1, 2, 3) a rozdíly mezi nimi
#### Základní zprávy IGMP a jejich smysl
- Router:
	- **Membership report** - koncový stroj chce odebírat z multicast skupiny
	- **Leave group** - vymaže záznam z tabulky, pokud není nikdo, kdo chce odebírat multicast ze skupiny
- Koncoví klienti:
	- **Group sepcific query** - pokud může existovat koncový stroj, co chce odebírat, tato zpráva toto ověřuje
	- **General query** - pokud je v síti alespoň jeden uzel, co chce odebírat z jakékoli skupiny, pošle jeden ze skupiny zprávu (stačí od jednoho)

### RPF podmínka
Multicast lze přijmout a přeposlat, iff unicastová směrovací tabulka má záznam se souhlasícím rozhraním příchozího multicastu se záznamem unicastové tabulky. Jinak se packety zahodí.

### Význam záznamů (S,G) a (*,G)
- **Zdrojově specifický záznam** (S, G) - záznamy na routerech tvoří nejkratší distribuční strom od zdroje ke příjímačům
- **Zdrojově nespecifický záznam** (\*, G) - souvisí se sdílenou částí distribučního stromu mezi RP routerem (PIM-SM)

### Funkce PIM-DM (hustý PIM)
- Pro síť, kde zdroje a příjímače jsou blízko u sebe (málo příjímačů odmítá)
- Broadcastuje první router připojený ke zdroji multicastu na všechna svá aktivní rozhraní
#### Zprávy PIM-DM a jejich význam (Hello ,Join/Prune, Graft, GraftAck, Assert)
- **Prune** - odříznutí části větve routerem, který nemá ve větvi aktivního příjímače
- **Graft** - router naroubuje zpět odřízlou větev

### Funkce PIM-SM (řídký PIM)
- Pro síť, kde na cestách je mnoho routerů, jež nechtějí odebírat multicast
	- => vhodný pro obrovské sítě, používanější
- Multicast do větví probíhá jen na vyžádání od sousedního routeru
#### Zprávy PIM-SM a jejich význam (Hello, Join/Prune, Graft, GraftAck, Assert,
Register, Register-Stop)
???
#### Smysl použití a volba Rendezvous bodu (RP)
- První router, přes který probíhá přenos -> *první fáze*
- Později se může nalést rychlejší cesta nevedoucí přes RP, prostřednictvím nového distribučního stromu od příjemce -> *druhá fáze*


## IPv6
- více úrovní adresní hierachie
- jednotné schéma pro vnitřní sítě a internet (no NAT)
- automatická konfigurace uzlů, optimalizace routování, QoS
- zvýšení bezpečnosti - šifrování, autentizace, sledování cesty k odesílateli

### Změny v IPv6 vztažmo k IPv4, Struktura IPv6 hlavičky a rozdíl oproti IPv4
- Nemá *Header Checksum* - vyšší vrstvy ho musely přepočítávat
- Nemá *Fragmentation Offset* - fragmentace je defaultně zakázána, je optional
- *Next Header* - ukazuje na adresu dalšího (optional) headeru
- *Type of service* -> *Traffic Class*
- *TTL* -> *Hop Limit*
- *Total Length* -> *Paylod Length*
- Přibyl *Flow Label* - pojí dohromady flow (jako FEC): SA, DA, SP, DP, TCP/UDP
	- Problém se šifrováním - krom SA a DA se zbytek šifruje
 
### IPv6 unicast, anycast a multicast
???

### Formát a zápis IPv6 adresy a použití znaku „:“
Formát - `identifikátor sítě  :  1/2 MAC  FF:FE  1/2 MAC`
- IPv6 má 7 :
- `FF:FE` je vložka od EUI-64 (Extended Unique Identifier)

Speciální adresy:
- `::/128` nedefinovaná adresa
- `::1/128` lokální smyčka (loopback)
- `FC00::/7` unikátní lokální adresy
- `FE80::/10` lokální linkové adresy
- `FF00::/8` skupinové adresy
- `2000::/3` globální individuální

Dvojtečka:
- Rozděluje 32 bitů
- Pokud je prázdné, jsou to 0
- Leading zeros ommited

### Použití jednotlivých typů IPv6 adresy (např. link-local, multicast, unikátní lokální apod.)
- **Lokální linkové** - počátač si je generuje sám a dokáže ověřit jednoznačnost,  využívá DHCP
- **Globální individuální**
	- Globální směrovací prefix -> adresa ISP
	- Identifikátor podsítě -> 16 b pro jednotlivé identifikace podsítí
	- Identifikátor rozhraní -> mapovaná MAC

### Autokonfigurace IPv6 adresy (stateles, statfull, RA, DHCPv6)
- Implementace ICMPv6 povinná v každém zařízení podporující IPv6
- Od IPv4 počítač připojený k síti automaticky vygeneruje adresu
- **Statefull** - přes server DHCPv6 spravující konfigurační parametry
- **Stateless** (**SALLC**) - lokální router rozesílá parametry (**RA**) do všech sítí, klient si je odposlechne nebo o ně požádá (**RS**)

### Proces alokace IPv6 adresových prefixů (ICANN, RIR, ISP, koncová síť)

### Způsoby přiřazení IPv6 (SLAAC, DHCPv6), DNS IPv6 záznamy
viz Statefull X Stateless

### Tunelovací metody a jejich použití
#### Obecný princip tunelovacích metod
#### Princip funkce statického IPv6 tunelu v IPv4 síti