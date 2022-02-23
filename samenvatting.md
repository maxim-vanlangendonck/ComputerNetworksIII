# Computer Networks III
| Inhoudstafel  |
| :---  |
| [SRWE](#srwe)
| 14.[Routing Concepts](#module-14-routing-concepts)    |
| 15.[Ip Static Routing]()  |
| 16.[Troubleshoot Static and Default Routes]() |
| [ENSA]()  |
| 1.[Single-Area OSPFv2 Concepts]() |
| 2.[Single-Area OSPFv2 Configuration]()    |
| 3.[Network Security Concepts]()   |
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