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


### Weppipalvelimen etusivu joka ei vaadi sudo oikeuksia

- Muuttaakseni Apache2 oletus aloitus-sivua joudun muuttamaan apache perusasetuksia /etc/ kansiossa.
- `/etc/apache2/sites-available` Hakemistosta löytyy muutettava tiedosto
- Luon hakemistoon uuden tiedoston nimeltä Frontpage.conf. TÄmä toiminto vaati sudo oikeuksia
                    
                    sudo micro frontpage.conf

- Hakemistosta löytyy 2 muuta tiedostoa: 
          - `000- default.conf` Oletan että tässä tiedostosta löytyy apachen perusasetukset kuten mihin logit tallennetaan
          - `default-ssl.conf`. Oletan että tässä tiedostossa löytyy weppipalvelin SSL(secure socket layer) asetukset.
- Juuri luomani tiedostoon 'frontpage.conf' kirjoitan mihin tiedostopolkuun palvelimen kotisivu viittaa.

                    <VirtualHost *:80>
	                DocumentRoot /home/fredrik/public_sites/
	                            <Directory /home/fredrik/public_sites/>
		                   require all granted
	                              </Directory>
                     </VirtualHost>
- Tiedostossa kerron palvelimelle että se etsii aloitussivua `home/fredrik/public_sites/` hakemistosta aloitussivua. Oletuksena palvelin etsii tiedostoa nimeltä index.html
- Olen luonnut normaalikäyttäjänä hakemistoon `/home/fredrik/public_sites` normaalina käyttäjänä ilman `sudo` oikeuksia tiedoston nimeltä index.html
![public_sites](https://user-images.githubusercontent.com/122887178/216674794-217ffcd2-d444-4cae-b4d3-488405889ae0.jpg)

- Nyt normaalikäyttäjä pystyy muokkaamaan palvelimen tiedostoja, eikä se vaadi SUDO oikeuksia


### Virheiden luominen

Etsiäkseni virheitä luon ensin sellaisen.

- Tiedostoon `/etc/apache2/available-sites/frontpage.conf` teen kirjoitusvirheen. Muutan hakemiston `public.sites` kun sen pitäisi olla `public_sites`. TÄmä toiminto vaati sudo oikeuksia

                              sudoedit frontpage.conf

- Jotta muutokset tulee voimaan käynnistän apach2 prosessin uudestaan

                              sudo systemctl restart apache2
                              
- Kun käytän `curl localhost` saan seuraavan vastauksen

                               <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
                              <html><head>
                              <title>403 Forbidden</title>
                              </head><body>
                              <h1>Forbidden</h1>
                              <p>You don't have permission to access this resource.</p>
                              <hr>
                              <address>Apache/2.4.54 (Debian) Server at localhost Port 80</address>
                              </body></html>
 ### Vian selvittäminen
                           
- Komenolla `sudo tail -1 /var/log/apache2/error.log` saan apache2 vikalokin viimeisimmän vikakoodin esille. Alla on vikaloki.


                    [Fri Feb 03 20:09:12.882654 2023] [authz_core:error] [pid 1865:tid 140494251837184] [client ::1:38748] AH01630: client denied by server                               configuration: /home/fredrik/public.sites

- Vikalokissa selviää että palvelin estää käyttäjää saamasta pyyntöönsä. Lisäksi lokissa lukee mistä konfiguraatiosta on kyse.
- Kotihakemistosta ei löydy kansiota nimeltä `/public.sites`
 ![vika1](https://user-images.githubusercontent.com/122887178/216678024-5b88bfd7-a5c6-41f9-b97d-7101c92855e0.jpg)
 
 - Komenolla `sudo apache2ctl configtest` saan selkeämmän vastauksen ongelmaan:

                              fredrik@hiekkis:~$ sudo apache2ctl configtest
                              AH00112: Warning: DocumentRoot [/home/fredrik/public.sites/] does not exist
                              AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
                              Syntax OK
                              
- Virheloki kertoo suoraan että /public.sites ei ole olemassa.
- Toinen virhe johtunee siitä että loopback osoite ei ole hyväksytty domain nimi. Virheloki antaa myös suoraan ehdotuksen mitä voin tehdä asialle.

### Lähteet 
https://httpd.apache.org/docs/2.4/getting-started.html

https://terokarvinen.com/2023/linux-palvelimet-2023-alkukevat/

https://httpd.apache.org/docs/current/vhosts/name-based.html


