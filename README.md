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
