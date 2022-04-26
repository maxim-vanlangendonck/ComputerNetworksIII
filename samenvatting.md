# Computer Networks III
| Inhoudstafel  |
| :---  |
| [SRWE](#srwe)
| 14.[Routing Concepts](#module-14-routing-concepts)    |
| 15.[Ip Static Routing](#module-15-ip-static-routing)  |
| 16.[Troubleshoot Static and Default Routes](#module-16-troubleshoot-static-and-default-routes) |
| [ENSA]()  |
| 1.[Single-Area OSPFv2 Concepts](#module-1-single-area-ospfv2-concepts) |
| 2.[Single-Area OSPFv2 Configuration](#module-2-single-area-ospfv2-configuration)    |
| 4.[ACL Concepts](#module-4-acl-concepts) |
| 5.[ACL for IPv4 Configuration](#module-5-acl-for-ipv4-configuration) |
| 6.[NAT for IPv4](#module-6-nat-for-ipv4) |
# SRWE
# Module 14: Routing Concepts
## 14.1 Path Determination
### 14.1.1 De 2 functies van een Router
- De 2 primaire functies van een router:
  - het beste pad vastleggen voor het forwarden van pakketten obv de informatie uit de routing tabel
  - het forwarden van de pakketten naar hun bestemming

### 14.1.3 Het beste pad is gelijk aan het langste match
- Het beste pad is de langste match in de routing tabel
- de routing tabel bevat de route entries d

### 14.1.6 Het opmaken van de Routing Tabel
- Direct geconnecteerde netwerken:
- Remote netwerken
- Standaard route

![Het opmaken van de Routing Table](img/BuildingRoutingTable.png)

## 14.2 Packet Forwarding
### 14.2.1 Packet Forwarding Decision Process
1. De data link frame met de geëncapsuleerde IP pakket komt aan op de ingress interface
2. De router examineert de bestemmings IP adres in de pakket header en bekijkt de IP routing tabel
3. De router zoekt de langste match prefix in de tabel
4. De router encapsuleert de pakket in een data link frame en forward dit via de egress interface
   
- achter het beslissen van het beste pad, kan de router het volgende doen
  - Het pakket forwarden op een apparaat die direct geconnecteerd is het op netwerk
  - het pakket forwarden naar een Next-Hop Router
  - Het pakket droppen - er is geen match in de Routing Tabel

### 14.2.2 Packet Forwarding Mechanisms
- de primaire verantwoordelijkheid van de pakket forwarding functie is het encapsuleren van pakketten in juist appro data link frame type voor de uitgaande interface
- De Router ondersteunt de volgende 3 pakket forwarding mechanismen
  - Process switching
  - Fast switching
  - Cisco Express Forwarding (CEF)
- **Process Switching**:
  - een ouder pakket forwarding mechanisme
  - wanneer een pakket arriveert op een interface, wordt deze doorgestuurd naar een control paneel waar de CPU het bestemmingsadres matcht met een entry in zijn Routing Tabel. Daarna bepaald hij de exit interface en forward het pakket.
  - De router doet dit voor alle pakketten, zelfs als de bestemming in dezelfde stream aan pakketten zit.
![Process Switching](img/ProcessSwitching.png)
- **Fast Switching**:
  - ouder maar beter mechanisme dan Process Switching
  - gebruikt een fast-switching cache om de next-hop informatie in op te slaan
  - wanneer een pakket arriveert op een interface, dan wordt het doorgestuurd naar de control plane waar de CPU zoekt voor een match in de fast-switching cache.
  - als het niet hier is, dan wordt het geprocess-switched
![Fast Switching](img/FastSwitching.png)
- **Cisco Express Forwarding (CEF)**:
  - het meeste recente en standaard Cisco IOS packet forwarding mechanisme
  - CEF maakt een Forwarding Information Base (FIB) en een aangrenzende tabel
  - de tabellen entries zijn niet packet-triggered zoals bij Fast Switching => maar change-triggerd (zoals wanneer een verandering is in de netwerk topologie)
  - wanneer een netwerk convergeerd, de FIB en de aangrenzende tabel bevat alle informatie dat een router zou moeten mee rekening houden wanneer hij een pakket zou forwarden
  ![Cisco Express Forwarding CEF](img/CiscoExpressForwarding.png)

## 14.3 Basic Router Configuration Review
### 14.3.1 Topologie
### 14.3.2 Configuratie commando's
![Configuratie commando's](img/RouterConfiguratieCommandos.png)
### 14.3.3 Verificatie Commando's
- `show ip interface brief`
- `show running-config interface <interface-type number>`
- `show interfaces`
- `show ip interface`
- `show ip route`
- `ping`

### 14.3.4 Filter commando output
- filterting commando's kunnen gebruikt worden om bepaalde uitput te krijgen, hiervoor hebt je | pipe karakter nodig
- bepaalde filter parameters
  - section
  - include
  - exclude
  - begin
## 14.4 IP Routing Table
### 14.4.1 Route Sources
- een routing tabel bevat een lijst aan routes 
- de bron van deze informatie komt van:
  - Direct geconnecteerde netwerken
  - statische routes
  - dynamische routing protocollen
- de bron van iedere route in de tabel is geïdentificeerd met een code:
  - **L**: identificeert een adres vastgelegd aan een router interface
  - **C**: identificeert een direct geconnecteerd netwerk
  - **S**: identificeert een statische route die gemaakt is om een specifiek netwerk te reachen
  - **O**: identificeert een dynamisch geleerd netwerk van een andere router door gebruik te maken van OSPF routing protocol
  - *: deze route is een kandidaat voor een standaard route

### 14.4.2 Routing tabel principes
- 3 routing table principes
  - iedere router maakt een beslissing alleen, gebaseerd op de informatie dat hij heeft in zijn eigen routing tabel
  - de informatie in een routing tabel van 1 router moet niet direct dezelfde zijn als de routing tabel van een andere router
  - routing informatie over een pad 

### 14.4.3 Routing Table Entries
1. **Route Source**: geeft weer hoe de route geleerd geweest is
2. **bestemming netwerk (prefix en prefix lengte)**: geeft het adres weer van het remote netwerk
3. **administratieve afstand**: geeft de betrouwbaarheid weer van de route bron. Hoe lager de waarde geeft de preferred route bron weer. deze is gebaseerd op welke routing protocol er gebruikt werd
4. **Metric**: geeft de waarde voor . De berekening is gebaseerd op de kost van een bepaalde route
5. **Next-hop**: het ip adres voor de volgende router naar welk het pakket 
6. **Route timestamp**: geeft de tijd weer sinds wanneer de route aangeleerd werd
7. **exit interface**: geeft de egress interface weer dat gebruitk wordt voor uitgaande pakketten om hun finale bestemming te bereiken

![Routing Tabel Entries](img/RoutingTableEntries.png)

### 14.4.4 Directly Connected Networks
- voordat een router kan leren over een remote netwerk, moet deze minstens 1 actieve interface hebben met een IP adres en subnet mask
- dit staat bekend als een direct verbind netwerk of direct geconnecteerde route
- routers kunnen een direct verbinde route toevoegen aan zijn routing tabel wanneer een interface geconfigueerd is met een IP adres en wanneer deze actief is
- heeft als status code 'C'
- de routing tabel kan ook een lokale route bevatten, deze wordt aangeduid met de status code 'L'
- voor IPv4 lokale routes is de prefix lengte /32 en voor IPv6 lokale routes is de prefix lengte /128, dit betekend dat de bestemmings ip adres van het pakket altijd gelijk moet zijn aan de bits in de lokale route

### 14.4.5 Statische routes
- statische routes hebben 2 primaire gebruiken
  - het geeft een gemakkelijk onderhoud van de routing tabel in kleinere netwerken
  - het geeft route van en naar stub netwerken. Een stub netwerk is een netwerk die bereikt wordt door 1 route, en de router heeft enkel maar 1 buur
  - het gebruikt een single default route om het pad naar elk netwerk dat niet 
### 14.4.6 Statische routes in de IP Routing tabel

### 14.4.7 Dynamic Routing Protocols
- Dynamic Routing Protocols worden gebruikt door routers om informatie over de bereikbaarheid en de status van remote networks te delen
- DRP doen verschillende activiteiten zoals:
  - network discovery
  - maintaining routing tables
  - ....
### 14.4.8 Dynamic Routes in the Routing Table

### 14.4.9 Default Route
- De default route specificeert een next-hop router die gebruikt moet worden wanneer de routing table niet de specifieke route heeft dat overeenkomt met de bestemming IP adres
- deze kan ofwel een statische route zijn of een route die aangeleerd werd door Dynamic Routing Protocol
- een default route heeft een IPv4 route entry van 0.0.0.0/0 of een IPv6 route entry van ::/0
### 14.4.10 Structure of an IPv4 Routing Table

### 14.4.11 Structure of an IPv6 Routing Table

### 14.4.12 Administrative Distance
- een route entry voor een specifiek netwerk adres kan alleen maar 1 keer voorkomen in een Routing table
- Cisco IOS maakt gebruikt van Administrative Distance (AD) om te bepalen welke route er geïnstalleerd zal worden in de IP routing table. De AD toont de betrouwbaarheid van de route
- hoe lager de AD, hoe betrouwbaarder de route is

![Administrative Distance](img/AdministrativeDistance.png)

## 14.5 Static and Dynamic Routing
### 14.5.1 Static of Dynamic?
- de meeste netwerken gebruiken een combinatie van de twee
- **Static Routes**
  - Static routes worden het meest gebruikt in volgende scenario's
    - als een default route om pakketten te forwarden naar de ISP
    - voor routes buiten het routing domein en die niet aangeleerd zijn door dynamic routing protocol
    - wanneer een netwerk admin een 
    - voor routing tussen stub netwerken
- **Dynamic Routing**
  - Dynamic routing protocols worden geïmplementeerd in ieder type van netwerk dat bestaat uit 1 of meerdere routers
  - Dynamic Routing protocols zijn schaalbaar en automatisch 
  - Deze worden gebruikt in volgende scenario's:
    - in netwerken dat bestaan uit meer dan 1 routers
    - wanneer er een verandering in de netwerk topologie is
    - voor schaalbaarheid

![static vs dynamic](img/StaticVSDynamic.png)

### 14.5.2 Dynamic Routing Evolution
- Dynamic Routing protocols worden al in netwerken gebruikt sinds 1980s

![Evolution of Dynamic Routing Protocols](img/EvolutionOfDynamicRoutingProtocols.png)

- 2 soorten
  - IGPs(Interior Gateway Protocols): wordt gebruikt voor het uitwisselen van informatie in 1 routing domein die beheerd wordt door 1 organisatie
  - EGP(Exterior Gateway Protocol): wordt gebruikt voor het uitwisselen van informatie tussen meerdere en verschillende organisatie's, die bekend staan als autonomous systems (AS)
    - wordt vaak gebruikt door ISP om pakketten over het internet te versturen
![gebruik van Dynamic Routing Protocols](img/DynamicRoutingProtocols.png)

### 14.5.3 Dynamic Routing Protocol Concepts
- een routing protocol is een set van processen, algoritmen en berichten dat gebruikt wordt voor het uitwisselen van routing informatie en het vullen van de routing table met de keuze voor het beste pad
- de bedoeling van dynamic routing protocols zijn:
  - het ontdekken van remote netwerken
  - het onderhouden van routing informatie
  - het kiezen van het beste pad naar bestemming netwerken
  - de mogelijkheid voor het vinden van een nieuw beste pad als het huidige pad niet meer beschikbaar is
- de belangrijkste onderdelen van dynamic routing protocols zijn:
  - **data structuren**: RP gebruiken vaak tabellen of databanken voor hun operaties. Deze worden opgeslagen in het RAM
  - **Routing Protocol messages**: RP gebruiken verschillende soorten berichten om hun taken te voldoen
  - **algoritme**: een algoritme is een lijst aan steppen om een taak te vervolledigen. RP gebruikt algoritmen om hun taken te doen

### 14.5.4 Best Path
- Het beste pad wordt geselecteerd door een RP gebaseerd op de waarde of metric dat gebruikt wordt om de afstand te bepalen om een netwerk te bereiken

![Best Path](img/BestPath.png)

### 14.5.5 Load Balancing
- wanneer een router 2 of meerdere paden heeft met dezelfde metric kost, dan forward de router de pakketten via de beide paden op hetzelfde moment. Dit noemt equal cost load balancing
  - de routing tabel bevat de single destination network, maar heeft meerde uitgang interfaces
  - als het correct geconfigueerd is, load balancing kan effectiviteit en performantie verbeteren van het netwerk
  - equal cost load balancing wordt automatisch geïmplementeerd door dynamic routing protocol. Het wordt aangezet met static routes wanneer er meerdere static routes naar hetzelfde netwerk zijn

# Module 15: IP Static Routing
## 15.1 Static Routes
### 15.1.1 Types of Static Routes
- Static routes worden vaak geïmplementeerd op een netwerk, dit is zo zelfs als er een dynamic routing protocol geconfigueerd is
- static routes kunnen geconfigueerd worden voor IPv4 of IPv6. Beide protocollen supporten de volgende types van static routes:
  - standard static route
  - default static route
  - floating static route
  - summary static route

### 15.1.2 Next-Hop Options
- Wanneer een static route geconfigueerd wordt, kan de next hop geïdentificeerd worden door een IP adres, uitganginterface of beide.
- Hoe de bestemming is gespecificeerd, maakt 1 van de volgende types van static routes:
  - **Next-Hop Route**: alleen een next-hop ip adres is meegegeven
  - **Directly connected static route**: alleen de uitgangsinterface
  - **fully specified static route**: beide

### 15.1.3 IPv4 Static Route Command
- de IPv4 static routes worden geconfigueerd door het globale commando:
  - `ip route <network-address> <subnet-mask>`

### 15.1.4 IPv6 Static Route Command
- IPv6 static routes worden geconfigueerd door het globale commando:
  - `ipv6 route <ipv6-prefix/prefix-length>`

### 15.1.5 Dual-Stack Topology


## 15.2 Configure IP Static Routes
### 15.2.1 IPv4 Next-Hop Static Route

## 15.3 Configure IP Default Static Routes
### 15.3.1 Default Static Route
- een standaard route is een statische route dat alle pakketten matcht

# Module 16: Troubleshoot Static and Default Routes


# ENSA
# Module 1: Single-Area OSPFv2 Concepts
## 1.1 OSPF Features and Characteristics
### 1.1.1 Introduction to OSPF
- OSPF is een link-state routing protocol dat gemaakt is als alternatief voor de afstand vector Routing Information Protocol (RIP).
- OSPF heeft grote voordelen in vergelijking met RIP, het heeft snellere convergentie en het schaalt beter bij het vergroten van het netwerk
- OSPF is een link-state routing protocol dat het concept van area's gebruikt. Een netwerk administrator kan een routing domein onderverdelen in distinct area's dat helpt bij het controleren van de routing update traffic
- een link is een interface op een router, een netwerk segment dat 2 routers connecteert met elkaar, of een stub netwerk zoals een Ethernet LAN dat geconnecteerd is met 1 router
- de informatie over de status van de link staat bekend als de link-state. Alle link-state informatie bestaat uit de netwerk prefix, prefix lengte, en kost

### 1.1.2 Onderdelen van OSPF
- alle routing protocollen delen dezelfde onderdelen. Zij gebruiken routing procotol berichten om routing informatie te delen. De berichten helpt het opbouwen van data structuren
- Routers die OSPF gebruiken, wisselen berichten uit om de routing informatie te verzamelen door gebruik te maken van 5 types van pakketten:
  - hello packet
  - database description packet
  - link-state request packet
  - link-state update packet
  - link-state acknowledgement packet
- Deze pakketten worden gebruikt om buurrouters te identificeren, om de routing informatie te verzamelen en om de routing informatie te versturen.

![components of OSPF](img/componentsOfOSPF.png)

- De router maakt een topologie tabel, door gebruik te maken van de berekingen gebaseerd op Dijkstra Shortest Path first Algorithm (SPF). De SPF algoritme is gebaseerd op de cummulatieve kost om de bestemming te bereiken
- Het SPF algoritme maakt een SPF tree door een router aan de root van de tree te zetten en te berekenen de kost van de root tot de bestemming. De SPF tree wordt dan gebruikt om de beste routes te berekenen. OSPF plaats de beste routes in de forwarding database, die gebruikt worden om de routing tabel aan te maken

### 1.1.3 Link-state Operation
- om routing informatie te behouden, OSPF router compleet een generieke link-state routing proces om een staat van convergentie te bereiken.
- De volgende link-state routing stappen worden gecompleet door een router
  1. Breng buren in de buurt
  2. link-state advertisements uitwisselen
  3. Bouw de linkstatusdatabase
  4. Voer het SPF-algoritme uit
  5. Kies de beste route

### 1.1.4 Single-Area and Multi-Area OSPF
- om een OSPF efficiënter en schaalbaarder te maken, ondersteunt OSPF een hierarchische routing door gebruik te maken van area's.
- een OSPF area is een groep van routers dat dezelfde link-state informatie deelt in hun LSDB.
- OSPF kan op 2 manieren geïmplementeerd worden:
  - **Single-Area OSPF**: alle routers zijn in 1 area, best practice is om area 0 te gebruiken
  - **Multi-Area OSPF**: OSPF wordt geïmplementeerd door gebruik te maken van meerdere area's, in een hiërarchische structuur. Alle areas moeten geconnecteerd worden met de backbone area (area 0). Routers interconnecteren 

### 1.1.5 Multiarea OSPF
- de hiërarchische topologie design opties met multiarea OSPF voordelen geven:
  - **kleinere routing tabellen**: de tabellen zijn kleiner doordat er minder routing tabel entries zijn
  - **verminderde link-state update overhead**
  - **verminderde frequentie van SPF berekeningen**

### 1.1.6 OSPFv3
- OSPFv3 is een nieuwe versie van OSPF die gebruik maakt van IPv6
- OSPFv3 heeft dezelfde functionaliteit als OSPFv2, maar maakt gebruik van IPv6 netwerk laag transport. 

## 1.2 OSPF Packets
### 1.2.1 Types of OSPF Packets
![OSPF packet types](img/typesOfOSPFPakketten.png)

### 1.2.2 Link-State Updates
- LSUs worden ook gebruikt om OSPF routing updates te forwarden
- een LSU pakket kan 11 verschillende types van OSPFv2 LSAs bevatten
- LSU en LSA zijn vaak uitwisselbaar gebruikt, maar de correcte hiërachie is dat LSU pakketten LSA berichten bevatten. 

### 1.2.3 Hello Packet
- De OSPF Type 1 pakket is een Hello packet.
- hello packet wordt gebruikt voor het volgende:
  - OSPF buren ontdekken en buur adjacencies aan te maken
  - het aankondigen van parameters op welke twee routers 
  - het aanmaken van een Designated Router (DR) en Backup Designated Router (BDR) on multiacces netwerken zoals Ethernet. Point-to-point links zijn niet nodig voor DR of BDR

## 1.3 OSPF Operation
### 1.3.1 OSPF Operational States
![OSPF Operational States](img/OSPFOperationalStatesv1.png)
![OSPF Operational States](img/OSPFOperationalStatesv2.png)

### 1.3.2 Establish Neighbor Adjacencies
- om te bepalen of er een OSPF buur op de link is, the router stuurt een Hello pakket dat een router ID bevat. Het Hello pakket wordt verstuurd naar alle OSPF router
- de OSPF Router ID wordt gebruikt om de OSPF proces uniek te identificeren op iedere router in een OSPF Area. Een router ID is een 32-bit nummer geformateerd zoals een IPv4-adres.
- wanneer een buur OSPF-enabled router ontvangt een Hello pakket met een router ID dat niet in de lijst staat, dan wordt er een adjacency gemaakt met de routing waarvan het hello pakket kwam. 

![OSPF Neighbor Adjacencies](img/OSPFNeighborAdjacencies.png)

### 1.3.3 Synchronizing OSPF Databases
- Na de Two-Way state, routers . Dit is een 3 stap process:
  - **kies eerste router**: de router met de grootste router ID stuurt een DBD eerst.
  - **wissel DBDs uit**: 
  - **stuur een LSR**
- als alle LSRs uitgewisseld zijn, routers worden als gesynchroniseerd en in full state gezien. Updates (LSUs) worden verstuurd: 
  - wanneer er een verandern voorkomt
  - iedere 30 minuten

### 1.3.4 The Need for a DR
- Multiacces netwerken kan 2 challenges maken voor OSPF door de flooding van LSAs:
  - **het aanmaken van meerdere adjacencies**: een router kan meerdere adjacencies hebben met andere routers in de area
  - **extensive flooding of LSAs**: link-state routers overvloeden hun LSAs iedere keer dat er OSPF geïnitialiseerd wordt, of wanneer er een verandering in topologie is

### 1.3.5 LSA Flooding with a DR
- de verhoging van het aantal routers op een multiaccess netwerk, dit kan ook het aantal LSAs verhogen die uitgewisseld worden. 
- als ieder router in een multiaccess netwerk 
- op multiaccess netwerken, de OSPF selecteert een DR om de collectie- en distributiepunt voor alle LSAs die verzonden en ontvangen worden. Een BDR wordt ook aangeduid voor wanneer de DR faalt. Alle andere routers worden DROTHERs. Een DROTHER is een router dat geen DR noch BDR is. 

# Module 2: Single-Area OSPFv2 Configuration
## 2.1 OSPF Router ID
### 2.1.1 OSPF Reference Topology
### 2.1.2 Router Configuration Mode for OSPF
- door gebruik te maken van commando `router ospf <process-id>` wordt OSPFv2 geïnitialiseerd

### 2.1.3 Router IDs
- een OSPF router ID is een 32-bit nummer, voorgesteld als een IPv4-adres. Het wordt gebruikt om een OSPF router uniek te identificeren. 
- iedere router heeft een router ID nodig om mee te doen in het OSPF domein. Het kan automatisch ingesteld worden door de router of door de administrator.
### 2.1.4 Router ID Order of Precedence
- cisco routers leid het router ID af gebaseerd op 1 van deze 3 criteria, in deze volgorde:
  1. de Router ID is expliciet ingesteld via commando `router-id <router-id>`, dit is aanbevolen methode
  2. de router kiest het hoogste IPv4 adres van een configureerde loopback interface
  3. de router kiest het hoogste actieve IPv4, van een van zijn actieve interfaces. 

![Order of Precedence](img/orderOfPrecedence.png)

### 2.1.5 Configure a Loopback Interface as the Router ID
### 2.1.6 Explicitly Configure the Router ID
### 2.1.7 Modify a Router ID
- achter dat een router een router ID geselecteerd heeft, laat een actieve OSPF router het niet toe om de Router ID aan te passen tot dat de router gereload is of het OSPF process gereset is.
- het OSPF proces clearen is de aanbevolen methode om de Router ID te resetten

## 2.2 Point-to-Point OSPF Networks
### 2.2.1 The network Command Syntax
- je kan de interfaces specifiëren die aan point-to-point netwerk behoren door het commando network te configureren. Je kan ook OSPF configureren op de interface met het `ip ospf` commando.
- de basis syntax voor het 'network' commando:
  - `network <network-address> <wildcard-mask> area <area-id>`
- het `<network-address> <wildcard-mask>` syntax wordt gebruikt op OSPF aan te zetten op de interfaces
- het `area <area-id>` syntax wordt gebruikt om de OSPF netwerk te specificeren op welke area het OSPF netwerk geplaatst moet worden.

### 2.2.2 The Wilcard Mask
- de wildcard mask is meestal het inverse van het subnet mask die geconfigueerd is op de interface
- de gemakkelijkste methode om de wilcard mask te berekenen is door de network subnet mask te verminderen van 255.255.255.255

### 2.2.3 Configure OSPF Using the network Command
- in het routing configuration mode. Zijn er 2 manieren om

### 2.2.4 Passive Interface
- standaard, OSPF berichten worden verstuurd naar alle OSPF-enabled interfaces. Maar deze berichten moeten enkel maar verstuurd worden naar de interfaces dat geconnecteerd zijn aan andere OSPF-enabled routers
- het versturen van deze berichten hebben een invloed op het LAN op deze 3 manieren:
  - **inefficiënt gebruik van het bandbreedte**: het beschikbare bandbreedte wordt gebruikt voor het vesturen van onnodige berichten
  - **inefficiënt gebruik van resources**: alle apparaten op het LAN moeten de berichten processen en dan het berichten verwijderen
  - **verhoogd Security risk**: zonder de OSPF

### 2.2.5 Configure Passive Interface
- gebruikt het `passive-interface` commando om een interface te configureren als een passieve interface

### 2.2.6 OSPF Point-to-Point Networks
- 

### 2.2.7 Loopbacks and Point-to-Point Networks

## 2.3 Multiaccess OSPF Networks
### 2.3.1 OSPF Network Types
- een andere type dat gebruik maakt van het OSPF is het "multiaccess OSPF network"
- in een multiaccess OSPF netwerk is uniek dat 1 router de controle heeft voor de distributie van LSAs
- de router die de rol heeft, wordt vastgelegd door de netwerk administrator

### 2.3.2 OSPF Designated Router
- in een multiaccess netwerken, OSPF selecteert een DR en BDR. De DR is verantwoordelijk voor het verzameling en versturen van LSAs die verstuurt en ontvangen worden. De DR gebruikt de multicast IPv4-adres 224.0.0.5 die voor alle OSPF routers
- een BDR wordt geselecteerd als de DR faalt. De BDR luister passief en onderhoud een relatie met alle routers. 
- alle andere routers zijn DROTHERs (een router dat noch een DR is noch een BDR is). DROTHERs gebruikt een multiaccess adres 224.0.0.6 om OSPF pakketten te versturen naar de DR en de BDR

### 2.3.3 OSPF Multiaccess Reference Topology
### 2.3.4 Verify OSPF Router Roles
### 2.3.5 Verify DR/BDR Adjacencies
- om de OSPFv2 adjacencies te verifiëren, gebruikt het `show ip ospf neighbor` commando
- de staat van de buren in een multiaccess netwerken is als volgt:
  - **FULL/DROTHER**:
  - **FULL/DR**:
  - **FULL/BDR**:
  - **2-WAY/DROTHER**:

## 2.4 Modify Single-Area OSPFv2
### 2.4.1 Cisco OSPF Cost Metric
- routing protocollen gebruiken een metric die het beste pad vastlegt. OSPF gebruik kost als een metric, een lagere kost toont een beter pad
- De Cisco-kosten van een interface zijn omgekeerd evenredig met de bandbreedte van de
koppel. Daarom duidt een hogere bandbreedte op lagere kosten. De gebruikte formule
om de OSPF-kosten te berekenen is:
  - Cost = referentie bandbreedte / interface bandbreedte
- de referentie bandbreedte is 10^8 (100,000,000), hiervoor is het de formule:
  - Cost = 100,000,000 bps / interface bandbreedte in bps

![Cisco OSPF Cost Metric](img/ciscoOSPFCostMetric.png)

### 2.4.2 Adjust the Reference Bandwidth
- de kost waarde moet een integer zijn. 
- voor deze reden, alle interfaces die sneller zijn dan Fast Ethernet zullen dezelfde kost waarde hbebn van 1 als een Fast Ethernet interface
# Module 4: ACL Concepts

# Module 5: ACLs for IPv4 Configuration

# Module 6: NAT for IPv4