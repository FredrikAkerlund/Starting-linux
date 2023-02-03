# h6 Based



### Apache software foundation 2023: Getting started

- Apache HTTP-palvelimen asennus ja käyttöönotto sisältää perusasetusten määrittelyn ja konfiguroinnin httpd.conf-tiedostossa, tarvittaessa moduulien lisäämisen, virtuaalipalvelinten luomisen ja määrittelyn sekä tietoturvan parantamisen.
-  Asennusprosessi vaihtelee järjestelmästä riippuen ja on kuvattu Apache HTTP-dokumentaatiossa.
-   HTTP-palvelin voidaan käynnistää ja sen toimintaa voidaan testata selaimessa.

### Apache Software foundation 2023: Name based virtual host support
- Apache HTTP-palvelin tukee nimipohjaista virtuaalipalvelinta, jossa jokainen virtuaalipalvelin on määritelty omaan IP-osoitteeseen ja hostinimeen.
- Sivustojen jako eri virtuaalipalvelimiin tapahtuu nimipohjaisen välityksen avulla, joka mahdollistaa useiden sivustojen isännöinnin samalta palvelimelta .
- Sivustojen konfigurointi tehdään määrittelemällä virtuaalipalvelimet httpd.conf-tiedostossa tai erillisessä tiedostossa.
- Esimerkki konfiguroinnista.


          <VirtualHost *:80>
            # This first-listed virtual host is also the default for *:80
            ServerName www.example.com
            ServerAlias example.com 
            DocumentRoot "/www/domain"
            </VirtualHost>
            <VirtualHost *:80>
             ServerName other.example.com
             DocumentRoot "/www/otherdomain"
            </VirtualHost>



#### Lähteet
https://httpd.apache.org/docs/current/vhosts/name-based.html


https://httpd.apache.org/docs/2.4/getting-started.html
