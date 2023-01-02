# AV-alused

## 1. From PING to HTTP

Kasutatavad koodiread ning nende tõlgendus

1) ```sudo apt-get update && sudo apt-get upgrade``` *Uuendab ja installib vajalikud võrgu tööriistad. Cloud shell ühendas võrguga ning tõmbas alla vajalikud paketid, seejärel pakkis need lahti. Koduses arvutis võttis see päris kaua aega.*

2) ```sudo apt-get install netcat-openbsd tcpdump traceroute mtr``` *Installib kõik vajalikud tööriistad, mida selle kursuse jooksul kasutame*
3) ```ip addr show eth0``` *Näitab mingit IP aadressi, vastuseks tuli:* ```inet 172.17.0.4/16 brd 172.17.255.255 scope global eth0``` - **Mis on eth0?**
4) ```ping -c3 8.8.8.8``` *Ei lase pingiga* - ```command not found``` *Kasutasin Windowsi command promti, sain ühenduse 8.8.8.8-ga, hiljem sain tööle kasutades koodi: * ```sudo apt-get install iputils-ping```
5) ```printf 'HEAD / HTTP/1.1\r\nHost: www.udacity.com\r\n\r\n' | nc www.udacity.com 80``` *Käsk printis praeguse kellaaja, ühenduse, cache controlli, asukoha ning millal toimub aegumine* - **Mis asi täpsemalt aegub? Mida tähendab:** 
```alt-svc: h3=":443"; ma=86400, h3-29=":443"; ma=86400```
6) ```printf 'HEAD / HTTP/1.1\r\nHost: en.wikipedia.org\r\n\r\n' | nc en.wikipedia.org 80``` *Saame infot Wikipedia serverilt, ühendame kasutades netcat'i läbi port 80. Kasutame printf-ga saadud info HTTP-st ning nö "söödame" selle netcat'le sisse. HTTP requestina saime info en.wikipedia.org ning seejärel anname info netcat'le, et see edastaks selle info samamoodi en.wikipedia.org serverisse. Kogu selline info edasi andmine käib kasutades "|"*
7) ```man nc``` *Avab nc kasutusjuhendi. Client/server modeli juures on kirjas, kuidas panna nc valitud porti jälgima. Koodiks on:* ```nc -l 1234``` *Antud rea põhjal kuulatakse porti 1234, ainult üks programm saab korraga kuulata ühte porti*
8) ```printf "GET / HTTP/1.1\r\nHost: en.wikipedia.org\r\n\r\n" | nc en.wikipedia.org 80 > example.txt``` *Kasutame eelnevalt loodud käsurida, kuid nüüd salvestame saadud info .txt faili. Tegu on siinkohal juba GET requestiga, mitte HEAD requestiga.*
9) ```printf 'HTTP/1.1 302 Moved\r\nLocation: https://www.eff.org/' | nc -l 2345``` *Toimus ümbersuunamine EFF kodulehele*

## 2. Names and Addresses

1) DNS - Domain name service
2) ```host -t a google.com``` *Selle koodireaga saame teada google.com ip aadressi*

```google.com has address 173.194.69.113
google.com has address 173.194.69.139
google.com has address 173.194.69.100
google.com has address 173.194.69.101
google.com has address 173.194.69.102
google.com has address 173.194.69.138
```

3) ```dig google.com``` *Annab samamoodi informatsiooni nagu "host" käsk, kuid saadav info on inimesel raskemini loetav, mõeldud rohkem programmidele*
```; <<>> DiG 9.16.33-Debian <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 13943
;; flags: qr rd ra; QUERY: 1, ANSWER: 6, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             300     IN      A       142.251.31.138
google.com.             300     IN      A       142.251.31.102
google.com.             300     IN      A       142.251.31.100
google.com.             300     IN      A       142.251.31.113
google.com.             300     IN      A       142.251.31.139
google.com.             300     IN      A       142.251.31.101

;; Query time: 2 msec
;; SERVER: 169.254.169.254#53(169.254.169.254)
;; WHEN: Sat Oct 15 08:32:58 UTC 2022
;; MSG SIZE  rcvd: 135
```
## 3. Addressing and Networks

1) ```IPv4 address - 32 biti``` - *Rohkem kui 4,29 mld aadressi, kuid igale inimesele ei jagu neid*

*IP aadressil on nii host osa kui ka network osa - nende vahekorrast sõltub ühes networkis kasutatavate IP aadresside arv*

2) ```171.64.0.0/14``` - *Standfordi /14 netblock (8 fixed bits, 6 fixed bits + 2 free bits)*

*IPV4 probleem - liiga vähe võimalikke aadresse*
*IP aadressid ei kuulu hostidele, vaid pigem interface'idele - ühel arvutil võib olla mitu interface'i (nt wifi või kaabelühendus, VLAN*

*Linuxil näed kõiki interface kasutades ```ip addr show```*
*Mac arvutil ```ifconfig```*

*Windows arvutil näed seda kasutades ```ipconfig``` käsku Command Promptis*

*NÄIDE:*

```Connection-specific DNS Suffix  . : lan
   IPv6 Address. . . . . . . . . . . : 2001:7d0:8cac:6080:5e48:12f:dc4c:3218
   Temporary IPv6 Address. . . . . . : 2001:7d0:8cac:6080:e509:9bb7:908b:df8b
   Link-local IPv6 Address . . . . . : fe80::1cfc:c023:3287:113d%15
   IPv4 Address. . . . . . . . . . . : 192.168.2.136
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : fe80::36e3:80ff:fe97:3890%15
                                       192.168.2.1
```
                                       
*Default gateway leidmine Linuxis - ```ip route show default```*

*Näide:*
```default via 172.17.0.1 dev eth0```

*Keskmine ping 0.06 ms*

*NAT kasutatakse kodudes, kontorites, andmekeskustes jne, et leevendada aadresside nappust, kuid see ei ole lahendus*
*NAT = Network Address Translation*

*IPv6 on lahendus aadresside vähesusele - 128biti - näiteks ```2001:7d0:8cac:6080:e509:9bb7:908b:df8b```*

## 4. Protocol Layers

1) *Protocol layers: ```HTTP, TCP, IP```*

*HTTP kasutus - URLid, küpsised*

*TCP kasutus - pordid, sessioonid, striimid*

*IP kasutus - IP aadressid, paketid*

*Wifi kasutus - WPA paroolid, pääsupunktid*

2) ```sudo tcpdump -n host 8.8.8.8``` *Näitab kogu liiklust enda hosti ning 8.8.8.8 hosti vahel*

```listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
0 packets captured
0 packets received by filter
0 packets dropped by kernel
```

*Antud command näitaks midagi muud, kui toimuks ka pingimine*

3) ```sudo tcpdump -n port 53``` - *Näitab DNS liiklust*

4) *TCP Flags - näitavad TCP sisu*

```20:52:14.967707 IP (tos 0x0, ttl 64, id 28114, offset 0, flags [DF], proto TCP (6), length 846)
    cs-132706671858-default.980 > 10.76.249.8.57388: Flags [P.], cksum 0xb2aa (incorrect -> 0x1f66), seq 27950:28744, ack 93, win 501, options [nop,nop,TS val 2969354431 ecr 3904873767], length 794
```
    
*[P] - näitab, et packet on mingi andmeosa lõpus - näiteks HTTP request*

5) Mis põhjustab TCP time-out'i?: 
* Teine host lülitati välja
* Midagi juhtus netikaabliga
* Kui proovida ühendust serveriga mida ei eksisteeri

## 5. Big Networks

1) ```traceroute google.com```- *Näitab kõigi ruuterite IP aadresse, mida kasutatakse, et jõuda google.com hostini*
```traceroute to google.com (108.177.119.139), 30 hops max, 60 byte packets
 172.17.0.1 (172.17.0.1)  0.053 ms  0.017 ms  0.014 ms
 * * *
 142.250.237.204 (142.250.237.204)  1.988 ms 
 142.250.238.240 (142.250.238.240)  1.209 ms 
 142.250.238.236 (142.250.238.236)  1.258 ms
 172.17.0.1 (172.17.0.1)  0.053 ms  0.021 ms  0.025 ms
 ```
2) ```mtr google.com``` - *Põhjalikum ülevaade liiklusest valitud hostiga*

```cs-132706671858-default (172.17.0.4) -> google.com                                           2023-01-02T21:30:00+0000
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                                                             Packets               Pings
 Host                                                                      Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. 172.17.0.1                                                              0.0%   104    0.1   0.1   0.1   0.2   0.0
 2. (waiting for reply)
 3. 142.250.237.204                                                         0.0%   104    1.5   1.5   1.0  14.4   1.3
 4. 216.239.41.41                                                           0.0%   104    0.4   0.5   0.3   1.0   0.1
 5. (waiting for reply)
 6. (waiting for reply)
 7. (waiting for reply)
 8. (waiting for reply)
 9. (waiting for reply)
10. (waiting for reply)
11. (waiting for reply)
12. (waiting for reply)
13. (waiting for reply)
14. ei-in-f101.1e100.net                                                    0.0%   103    0.4   0.4   0.3   0.9   0.1
```

3) *Kuidas eristada seda, kes on sama avaliku aadressi taga?*

* Kasutajanimi
* Sessiooni küpsised
