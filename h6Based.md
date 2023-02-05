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
                              
- Virheloki kertoo suoraan että `/home/fredrik/public.sites/` ei ole olemassa.
- Toinen virhe johtunee siitä että loopback osoite ei ole hyväksytty domain nimi. Virheloki antaa myös suoraan ehdotuksen mitä voin tehdä asialle.


### 2 nimipohjaisen palvelimen luonti

- Aloitan lukemalla avoimesta verkosta oppaita ja keskustelufoorumista. etsin hakusanoilla ` How to make 2 name based servers on apache running on loopback address` 
- Löysin seuraavan sivun: `https://www.clickittech.com/tutorial/emulate-hosts-file-dns-trick/` seuraan sen ohjeita

			sudo micro /etc/hosts

-lisään tiedostoon uuden Host nimen

			# Host addresses
			127.0.0.1  localhost
			127.0.1.1  hiekkis
			127.0.0.1  foo.example.com ##Lisäsin tämän rivin
			::1        localhost ip6-localhost ip6-loopback
			ff02::1    ip6-allnodes
			ff02::2    ip6-allrouters
- Tallennan tiedoston ja kokeilen curlilla päästä sivulle

				curl foo.example.com
- Tämä onnistui suoraan. Oletin että olisi pitänyt käynnistää apache2 prosessi uudestaan mutta ei ollut tarvetta.

![foo example](https://user-images.githubusercontent.com/122887178/216754766-9d5a8755-6703-46ca-b3f9-7f8116f72ce4.jpg)

-   Seuraavaksi teen uuden host lisäyksen tiedostoon `/etc/hosts/`

				# Host addresses
				127.0.0.1  localhost
				127.0.1.1  hiekkis
				127.0.0.1  foo.example.com
				127.0.0.1  bar.example.com ##Lisäsin tämän rivin
				::1        localhost ip6-localhost ip6-loopback
				ff02::1    ip6-allnodes
				ff02::2    ip6-allrouters
- Sain saman tuloksen kun aikasemmin. 
- Lisäämällä nimiä `/etc/Hosts` tiedostoon simuloin DNS palvelua.
### Omien kotisivujen luominen omille sivustoille

- Kokeilin lisätä `/etc/apache2/sites-available/frontpage.conf` viittauksen toiseen kotihakemistoon foo.example.com sivustolle

						<VirtualHost *:80>
				DocumentRoot /home/fredrik/public_sites/
				<Directory /home/fredrik/public_sites/>
					require all granted
				</Directory>
			</VirtualHost>
			## Iisäsin tämän <foo.example.com:80>
				DocumentRoot /home/fredrik/public_sites1/
				<Directory /home/fredrik/public_sites1/>
					require all granted
				</Directory>
			</foo.example.com>	
- Käynnistän APache2 uudestaan
- Seuraavat virhe lokit tulivat suoraan vastaan: 
![Example virheloki](https://user-images.githubusercontent.com/122887178/216755256-09b5ee4c-2a07-4693-b830-c6910f753c54.jpg)
- Aikani ei riittänyt selvittää ongelmaa mutta oletan että viittaukset jotka tein `/etc/apache2/sites-available/frontpage.conf` on väärin tehty
- Tavoitteeni oli saada joka sivulle oma kotisivu hakemisto. Taitoni ja aikani ei tähän riittänyt.
- Poistin lisäykseni `/etc/apache2/sites-available/frontpage.conf` ja sivut toimivat taas

 ### Jatkumoa sivujen omien kotisivujen luomisessa
 
- Jatkoin ongelman ratkaisua myöhemällä ajalla:
- Löysin sivuston `https://opensource.com/article/18/3/configuring-multiple-web-sites-apache` missä kerrotaan miten lisätään kotihakemistoja eri sivustoille.
- loin hakemistoon `/etc/apache2/sites-available/` tiedoston nimeltä `foo.example.com.conf`
- Tiedostoon kirjoitin seuraavan:

			<VirtualHost *:80>
				ServerName foo.example.com
				DocumentRoot /home/fredrik/public_foo/
					<Directory /home/fredrik/public_foo>
					Require all granted
			    		</Directory>    
			</VirtualHost>
- Kokeilin toimiko muutokset.
- Muutokset ei aiheuttanut muutoksia kotisivuun. `foo.example.com` käytti samaa kotisivua kuin localhost
- Käytin komentoa `sudo a2ensite foo.example.com.conf`
- Käynnistin uudestaan apache palvelun `sudo Systemctl restart apache2`
- Tajusin että en ole luonnut kansiota `/home/fredrik/public_foo`. Loin sen ja lisäsin tiedoston nimeltä index.html hakemistoon.
- Käytin komentoa curl foo.example.com. Sain tuloksen

				fredrik@hiekkis:/etc/apache2/sites-available$ curl foo.example.com
				Foo.example.com kotisivu

- Tein samat toimenpiteet `bar.example.com` sekä `localhost` sivuille.
- Tässä lopputulos:
![Omat kotisivut eri hosteille](https://user-images.githubusercontent.com/122887178/216836952-3d1000bc-42ac-4e58-9049-f15b118a511d.jpg)

- Tehdessäni tätä "bonus lisätehtävää" törmäsin useaan ongelmaan. Ratkaisin nämä ongelmat samalla tavalla kuin tein kohdassa "Vian selvittäminen"
- Tein asiat nopeasti enkä kirjoittanut raporttia asiasta. Jälkeenpäin ajatellen olisi pitänyt jotta ongelmaa olisi voinnut ratkaista myöhemmin ja analysoida tarkemmin mitä tein
			

### Lähteet 
https://httpd.apache.org/docs/2.4/getting-started.html

https://terokarvinen.com/2023/linux-palvelimet-2023-alkukevat/

https://httpd.apache.org/docs/current/vhosts/name-based.html

https://www.clickittech.com/tutorial/emulate-hosts-file-dns-trick/

https://opensource.com/article/18/3/configuring-multiple-web-sites-apache


