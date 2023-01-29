# H4

### How to Handle Secrets on the Command Line

#### CARL TASHIAN 2021-03-12 , https://smallstep.com/blog/command-line-secrets/

- Blogipostaus devaajalta jossa hän kertoo mitä haavoittuvuuksia salaisilla tiedostoilla on eri ympäristöissä
- Hän selittää myös miten komentokehotetta voi käyttää hyvääkseen jotta saa selville käyttäjien "salaisia" tietoja
- Postauksessa Carl varoittaa miten välttää salassa pidettävien tiedostojen päätyvän komentoihin jotka on nähtävillä kaikilla


        "Avoid leaking the secret in the command string eg. with "$(< secret_file.txt)""
  
- Carl kertoo postauksessa seuraavan "Using environment variables for secrets is very convenient. And we don’t recommend it because it’s so easy to leak things"
- Se tarkoittaa, että käyttämällä ympäristömuuttujia salauksen tallentamiseen on helppo hallita ja käyttää näitä tietoja sovelluksessa. Kuitenkin, koska ympäristömuuttujat ovat helposti saatavilla ja niitä voidaan jakaa helposti, niiden käyttäminen salausten tallentamiseen on riskialtista ja suuria salaisuuksia ei tulisi tallentaa näin.



###  Syslog

<img width="753" alt="Untitled" src="https://user-images.githubusercontent.com/122887178/215346030-551c739d-dd75-4f9d-a910-2808ac8b5cee.png">

Logissa tapahtuu seuraavia asioita
-Aika: 29 JAN 19:49:34
- Mikä käyttöympäristö :Hiekkis
- Tämä systemlog kertoo, että systemd-palvelu (palvelujärjestelmä Linux-järjestelmissä) on suorittanut systemd-exit.service-palvelun onnistuneesti.
 Systemd-exit.service on systemd:n tarjoama palvelu, jonka tehtävänä on lopettaa ja sulkea järjestelmän palvelut ja resurssit ennen järjestelmän sammumista. Tämä logi    ilmoittaa, että tämä palvelu on suoritettu onnistuneesti ilman virheitä.
 
 ### Auth.log
 ![Auth](https://user-images.githubusercontent.com/122887178/215346408-16dad716-fc5e-47b2-bc84-021af5cd9594.jpg)

- Loki merkintä kertoo seuraavat asiast
- Aika: 29 JAN 20:04:14 
- Käyttoympäristö Hiekkis
- Sudo käyttöoikeuksia käytti käyttäjä fredrik
- TTY=pts/0 on terminaaliseuraantapalvelun (TTY) identifikaattori Linux-järjestelmissä.
- Pts tarkoittaa pseudoterminaalia, joka on emuloitu terminaali, joka mahdollistaa käyttäjän ja järjestelmän välisen vuorovaikutuksen. 0 on pseudoterminaalin yksilöllinen tunniste, joka erottaa sen muista samanaikaisista pseudoterminaaleista järjestelmässä. TTY=pts/0 viittaa siis yhteen käytössä olevista pseudoterminaaleista järjestelmässä.
-Terminaali kertoo että "root" käyttäjä suorittaa komennon "cat Auth.log

### Apache acces.log
![Apache acceslog](https://user-images.githubusercontent.com/122887178/215347207-18deb9a1-72aa-4dde-9d3d-cfac128f9255.jpg)

-Tämä on web-palvelinlokin merkintä, joka kuvaa yhden HTTP-pyynnön, joka on tehty verkkosivulle. Se sisältää seuraavat tiedot:

-IP-osoite: 127.0.0.1
-Käyttäjän tunniste: tyhjä
-Aikaleima: 27. tammikuuta 2023, klo 14:40:13 +0200
-Pyyntö: GET / HTTP/1.1
-Palvelimen vastaus: 200 (HTTP-tilakoodi, joka tarkoittaa onnistunutta pyyntöä)
-Vastauksen pituus: 3380 tavua
-Referer: Tyhjä
-User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
-Yhteenvetona tämä merkintä kertoo, että IP-osoitteesta 127.0.0.1 lähetettiin onnistunut HTTP-pyyntö verkkosivulle osoitteessa / ja vastauksena saatiin 3380 tavun pituinen sivu. Käyttäjän selain oli Mozilla Firefox versiossa 102.0.

### Apache2 error log
![Apache error log](https://user-images.githubusercontent.com/122887178/215347443-0a38d273-da03-4e77-92a3-6a3d3a119ddb.jpg)

- Tämä on Apache HTTP Serverin logimerkintä, joka kuvaa tapahtumaa, joka tapahtui Apache-palvelimella. Se sisältää seuraavat tiedot:

- Aikaleima: Perjantai 27. tammikuuta 2023, klo 16:39:41.551797
- Moduulin nimi: mpm_event
- Tason nimi: notice
- Prosessin tunniste (PID): 585
- Säikeen tunniste (TID): 139690521267520
- Viesti: AH00492: caught SIGWINCH, shutting down gracefully
- SIGWINCH tarkoittaa "Signal Window Change" eli ikkuna-muutos-signaalia. Se on Unix-järjestelmissä käytetty signaali, joka lähetetään ohjelmalle, kun käyttäjä muuttaa ikkunaan liittyviä asetuksia, kuten ikkunan kokoa tai sijaintia. Ohjelma voi käsittelä tämän signaalin ja sopeuttaa toimintaansa ikkunan muutosten mukaan.
- Yhteenvetona tämä merkintä kertoo, että Apache HTTP Server -prosessi (jota hallinnoi mpm_event-moduuli) vastaanotti SIGWINCH-signaalin (joka ilmaisee ikkunalaudan muutoksen) ja aloitti sulkeutumisprosessin gracefully (huolellisesti). Tämä on normaali tapahtuma, joka tapahtuu, kun ikkunalauta muuttuu esimerkiksi ikkunan koon muutoksen vuoksi.


### Itse tuottamat tapahtumat Apache 2 lokiin'

![Apachetapahtumat](https://user-images.githubusercontent.com/122887178/215347769-fc39fec7-b11e-4142-a8a8-9cc4d21f96f9.jpg)

#### Ensimmäinen tapahtuma
- Tämä on web-palvelinlokin merkintä, joka kuvaa yhden HTTP-pyynnön, joka on tehty verkkosivulle. Se sisältää seuraavat tiedot:

- IP-osoite: 127.0.0.1
- Käyttäjän tunniste: tyhjä
- Aikaleima: 29. tammikuuta 2023, klo 20:23:58 +0200
- Pyyntö: GET / HTTP/1.1
- Palvelimen vastaus: 200 (HTTP-tilakoodi, joka tarkoittaa onnistunutta pyyntöä)
- Vastauksen pituus: 3380 tavua
- Referer: Tyhjä
- User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
- Yhteenvetona tämä merkintä kertoo, että IP-osoitteesta 127.0.0.1 lähetettiin onnistunut HTTP-pyyntö verkkosivulle osoitteessa / ja vastauksena saatiin 3380 tavun pituinen sivu. Käyttäjän selain oli Mozilla Firefox versiossa 102.0.

#### Toinen tapahtuma
- Toisessa
- IP-osoite: 127.0.0.1, joka tarkoittaa, että pyyntö tuli samalta laitteelta, joka ajoi HTTP-palvelinta
- Käyttäjä: -, eli pyyntö ei ollut autentikoitu
- Aikaleima: 29. tammikuuta 2023, klo 20:23:58 +0200
- Pyyntö: "GET /teentuhmuuksiajotaensaatehda HTTP/1.1"
- Vastauskoodi: 404, joka tarkoittaa, että pyydettyä sivua ei löytynyt
- Vastauksen koko: 488 tavua
- Referer: -, eli pyyntö ei tullut linkin kautta toiselta sivulta
- User agent: "Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0", joka kuvaa selaimen ja sen version, jolla pyyntö tehtiin.
- Yhteenvetona tämä merkintä kertoo, että IP-osoitteesta 127.0.0.1 tehtiin pyyntö osoitteeseen "/teentuhmuuksiajotaensaatehda" HTTP-palvelimelle. Pyyntö ei löytänyt vastaavaa sivua, joten palvelin vastasi koodilla 404. Pyyntö tehtiin Mozilla Firefox -selaimella.
