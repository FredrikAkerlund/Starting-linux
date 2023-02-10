## h7 Real internet (tm)

### Tero Karvinen 2012: First steps on a virtual private server (VPS)


Tämä artikkeli kuvaa ensimmäisiä askeleita uuden virtuaalipalvelimen asennuksessa DigitalOceanilla:

Uuden palvelimen luominen
- Yhteyden luominen palvelimeen SSH:lla
- Tarvittavien ohjelmien asentaminen
- Vinkkejä turvallisuuden parantamiseen
- Tietoturvallisuuden parantaminen muuttamalla oletus SSH-porttia
- Sallittujen porttien ja protokollien määrittely
- Root-käyttäjän kirjautumisen estäminen
Artikkeli tarjoaa yleiskuvan siitä, miten virtuaalipalvelin voidaan asentaa ja miten sitä voidaan suojata alkuperäisestä asennuksesta alkaen.


- Lähde: https://terokarvinen.com/2017/first-steps-on-a-new-virtual-private-server-an-example-on-digitalocean/

### Oman palvelimen vuokraus Linode
- Valitsin Linode.com palveluntarjoajaksi
- Käytin tunnetun Youtube videotuottajan Linode promokoodi joka antaa minulle 100$ käytettäväksi 60 päiväksi.
- Youtube kanava: https://www.youtube.com/@NetworkChuck/
- Kirjaudun Linode palveluun:
- Klikkaan Create painiketta ja määrittelen mitä suorituskykyä haluan VPS palvelulta:
- Valitsen seuraavat:
    - Images: Debian 11
    - Region: Frankfurt DE (EU-central
    - Linode plan: Shared CPU: Nanode 1GB 
    - Label: Sandbox
    - Root Password: Generoin F-secure ID kanssa vahvan salasanan (25 merkkiä pitkä)
- Scrollaan sivun loppuun ja valitsen Create Linode.
- Hetken ajan päästä Linode etusivulle ilmestyy oma VPS statuksella RUNNING

### Oman palvelimen käyttöönotto 
- Otan SSH yhteyden windows terminaalinkautta:
                ssh root@143.42.57.220
- Terminaali antaa seuraavan varoituksen `The authenticity of host '143.42.57.220 (143.42.57.220)' can't be established.
ED25519 key fingerprint is SHA256:eYda/LYs+YIZf4mZLe8V7SVBuOjE/l8omQkc+2XESzo.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])?`
- Käytänössä terminaali kysyy luotatko internettiin koska kyseinen palvelin johon otan yhteyttä on täysin uusi eikä verifioitu.
- Syötän generoidun salasanan terminaaliin.
- Onnistuin luomaan yhteyden VPS!
                The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

                Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
                permitted by applicable law.
                root@localhost:~#
                
- Teen palomuuriin reiän portti 22 TCP liikenettä varten jotta SSH yhteys palvelimeen on mahdollista
              
              sudo ufw allow 22/tcp
                
- Palvelin ilmoittaa `ufw command not found`
- Päivitän repositoryn ja asennan ufw
              
              sudo apt update
              sudo apt install ufw
- Toistan edellisen komennon
                
              sudo ufw allow 22/tcp
- käynnistän palomuurin. Palvelin varoittaa että menetän SSH yhteyden mutta tein reiän palomuuriin porttiin 22 TCP liikenteelle. SSH yhteys pysyy

                sudo ufw enable
                
- Lisään palvelimelle käyttäjän `sandbox` ja lisään sen sudo ryhmään sekä adm ryhmään. adm ryhmä antaa käyttäjälle lukea eri tiedostoja /etc/ ja /var/ kansiossa.
    
                sudo adduser sandbox
                sudo adduser sandbox sudo
                sudo adduser sandbox adm
                
- Avaan toisen terminaalin ja kirjaudun sisään sillä palvelimelle uudella käyttäjällä
                
                ssh sandbox@143.42.57.220
                
- Syötän juuri syöttämäni salasanan ja pääsin palvelimelle sisään. Huom. Nyt terminaali on värillinen ja muistuttaa linux terminaalia.

![sandbox terminal](https://user-images.githubusercontent.com/122887178/217559036-36d6989d-195d-4d00-a431-6c6b14b1b685.jpg)

- Lukitsen root käyttäjän.

                root@localhost:/# sudo usermod --lock root
                
- Lisäksi disabloin SSH yhteyden root käyttäjälle.

                sudoedit /etc/ssh/sshd_config
                # Authentication:

                    #LoginGraceTime 2m
                    PermitRootLogin no
                    #StrictModes yes
                    #MaxAuthTries 6
                    #MaxSessions 10
                    
- Nyt on Rootkäyttäjän pääsy palvelimelle estetty ja uusi sudo käyttäjä luotu
- Varmistan tämän sulkemalla root ssh yhteyden ja yritän avata uutta istuntoa ROOT käyttäjänä
                
                Connection to 143.42.57.220 closed.
                PS C:\Users\Fredr> ssh root@143.42.57.220
                root@143.42.57.220's password:
                Permission denied, please try again.
                root@143.42.57.220's password:
                
- Palvelin estää tämän
- Viimeiseksi päivitän palvelimen `sandbox` käyttäjänä.
                
                sudo apt update
                sudo apt upgrade
                
- Palvelin on nyt otettu käyttöön ja perus suoja-asetukset asennettu.
                

### Apache2 weppipalvelimen asentaminen ja käyttöönotto

- Asennan apache2 ohjelman palvelimelle

                sudo apt install apache2
                
- Teen palomuuriin reiän portti 80 jotta pääsen käyttämään weppipalvelinta avoimen internetin kautta. Portti 80 käytetään HTTP protokollalle. Nyt oletamme että palvelin palvelee vain TCP liikenettä. Palvelimelle asenamme vain perus HTML sivuja

                sudo ufw allow 80/tcp
                
- Testaan palvelimen toimivuutta.

                curl 143.42.57.220
- Vastauksena saan apache2 oletus aloitussivun
- Seuraavaksi korvaan oletussivun
                
                sudo micro /var/www/html/index.html
                Poistan kaiken ja korvaan sen sanalla moi
- Testaan toimiko tämä
                
                curl 143.42.57.220
                Moi
- Testaan toisella tietokoneella pääsenkä palvelimeen käsiksi

![Apache2 omalla koneella](https://user-images.githubusercontent.com/122887178/217563270-77ff0422-71c0-4414-b834-050d5b4c56d3.jpg)

- Luon uuden dokumenttipolun niin että ei tarvitse `sudo` oikeuksia muokatakseen palvelimen sisältöä

                sandbox@localhost:/etc/apache2/sites-available$ micro frontpage.conf
- Luon tiedostoon ja lisään siihen seuraavan dokumentti viittauksen

            <VirtualHost *:80>
             DocumentRoot /home/sandbox/public.sites
                <Directory /home/sandbox/public.sites
                     Require all granted
                </Directory>
            </VirtualHost>
- Enabloin uuden konfiguraation
                
                sudo a2ensite frontpage.conf
                
- Käynnistän apache2 palvelimen uudestaan
                
                sudo systemctl restart apache2

- Sain virheilmoituksen:

            ● apache2.service - The Apache HTTP Server
                 Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
                 Active: failed (Result: exit-code) since Wed 2023-02-08 14:54:41 UTC; 25s ago
                   Docs: https://httpd.apache.org/docs/2.4/
                Process: 7564 ExecStart=/usr/sbin/apachectl start (code=exited, status=1/FAILURE)
                    CPU: 21ms

            Feb 08 14:54:41 localhost systemd[1]: Starting The Apache HTTP Server...
            Feb 08 14:54:41 localhost apachectl[7567]: AH00112: Warning: DocumentRoot [/home/sandbox/public.sites] does not exist
            Feb 08 14:54:41 localhost apachectl[7567]: AH00526: Syntax error on line 3 of /etc/apache2/sites-enabled/frontpage.conf:
            Feb 08 14:54:41 localhost apachectl[7567]: <Directory> directive missing closing '>'
            Feb 08 14:54:41 localhost apachectl[7564]: Action 'start' failed.
            Feb 08 14:54:41 localhost apachectl[7564]: The Apache error log may have more information.
            Feb 08 14:54:41 localhost systemd[1]: apache2.service: Control process exited, code=exited, status=1/FAILURE
            Feb 08 14:54:41 localhost systemd[1]: apache2.service: Failed with result 'exit-code'.
            Feb 08 14:54:41 localhost systemd[1]: Failed to start The Apache HTTP Server.
            
- Jatkan selvittelyä
- 2h jälkeen jatkoin vian selvittämistä
- Annoin komennon `sudo apache2ctl configtest` joka antoi minulle eväitä miten ratkaisen ongelman.

                AH00112: Warning: DocumentRoot [/home/sandbox/public.sites] does not exist
                AH00526: Syntax error on line 3 of /etc/apache2/sites-enabled/frontpage.conf:
                <Directory> directive missing closing '>'
                Action 'configtest' failed.
                The Apache error log may have more information.
                
- Luon kansion `/home/sandbox/public.sites` ja kansioon sisälle tiedoston `index.html` missä on sisältöä

                mkdir /home/sandbox/public.sites
                micro index.html
- Kokeilen toimiiko weppipalvelin

                curl 143.42.57.220
                curl: (7) Failed to connect to 143.42.57.220 port 80: Connection refused        
- Kokeilin käynnistää uudestaan apache2

                sudo systemctl restart apache2
                Job for apache2.service failed because the control process exited with error code.
                See "systemctl status apache2.service" and "journalctl -xe" for details.
- Korjaan `/etc/apache2/sites-enabled/frontpage.conf`. Aikasemmin `apache2ctl configtest` antoi virheilmoituksen syntaxissa rivillä kolme

                <VirtualHost *:80>
                    DocumentRoot /home/sandbox/public.sites
                     <Directory /home/sandbox/public.sites> ## Tästä puuttui sulku
                       Require all granted
                     </Directory>
                  </VirtualHost>
- Kokeilen uudestaan käynnistää apache2 processin 
- Tämä ei antanut virheilmoituksia.
- Kokeilen seuraavaksi kotisivujen toimivuutta

                curl 143.42.57.220
                Moi
- Weppipalvelin näyttää minulle alkuperäisen kotisivun joka sijaitsee `/var/www/public_html`

- Hakemistosta `/etc/apache2/sites-enabled` näen että 2 sivustoa on enabloiduttu
                
                sandbox@localhost:/etc/apache2/sites-enabled$ ls
                000-default.conf  frontpage.conf
- Poistan 000-default.conf enabloiduista sivuista

                sudo a2dissite 000-default.conf
- Käynnistän apachen uudestaan ja kokeilen muuttuiko kotisivu.
                
                sandbox@localhost:/etc/apache2/sites-enabled$ curl 143.42.57.220
                Normaalikäyttäjän etusivu
- Avaan saman kotisivun toisen koneen selaimella.
- Tämä toimii. Sivu näyttää nyt oikean muutetun kotisivun
- Lopuksi vaihdan kotisivun sisällön silmää mielyttäväksi.
                
### Murtoyritysten havaitseminen

- Aloitin etsimällä tieto netistä. Löysin sivun `https://www.tecmint.com/find-failed-ssh-login-attempts-in-linux/`
- Avaan tiedoston `/var/log/auth.log`
- Tiedostosta löytyy useita kirjautumis yrityksiä root käyttäjälle.

                Feb  8 17:38:12 localhost sshd[8536]: Connection closed by authenticating user root 69.50.138.96 port 49555 [preauth]
                Feb  8 17:38:12 localhost sshd[8499]: Connection closed by invalid user yan 69.50.138.96 port 49509 [preauth]
                Feb  8 17:38:12 localhost sshd[8498]: Connection closed by invalid user steam 69.50.138.96 port 49537 [preauth]
                Feb  8 17:38:13 localhost sshd[8513]: Connection closed by invalid user ly 69.50.138.96 port 49521 [preauth]
                Feb  8 17:38:13 localhost sshd[8512]: Connection closed by invalid user abc 69.50.138.96 port 49516 [preauth]
                Feb  8 17:38:13 localhost sshd[8500]: Connection closed by invalid user king 69.50.138.96 port 49534 [preauth]
                Feb  8 17:38:13 localhost sshd[8496]: Connection closed by invalid user admin 69.50.138.96 port 49532 [preauth]
                Feb  8 17:38:13 localhost sshd[8531]: Connection closed by invalid user oracle 69.50.138.96 port 49567 [preauth]
                Feb  8 17:38:13 localhost sshd[8505]: Connection closed by authenticating user daemon 69.50.138.96 port 49551 [preauth]
                Feb  8 17:38:13 localhost sshd[8497]: Connection closed by invalid user user 69.50.138.96 port 49523 [preauth]
                Feb  8 17:38:13 localhost sshd[8507]: Connection closed by invalid user odoo15 69.50.138.96 port 49507 [preauth]
                Feb  8 17:38:13 localhost sshd[8523]: Connection closed by invalid user jason 69.50.138.96 port 49533 [preauth]
                Feb  8 17:38:13 localhost sshd[8514]: Connection closed by invalid user emqx 69.50.138.96 port 49530 [preauth]
                Feb  8 17:38:13 localhost sshd[8525]: Connection closed by invalid user user 69.50.138.96 port 49545 [preauth]
                Feb  8 17:38:13 localhost sshd[8494]: Connection closed by invalid user guest 69.50.138.96 port 49529 [preauth]
                Feb  8 17:38:13 localhost sshd[8537]: Connection closed by invalid user tomcat 69.50.138.96 port 49558 [preauth]
                Feb  8 17:38:13 localhost sshd[8538]: Connection closed by invalid user posiflex 69.50.138.96 port 49549 [preauth]
                Feb  8 17:38:14 localhost sshd[8530]: Connection closed by invalid user data 69.50.138.96 port 49538 [preauth]
- Tästä voi vetää sen johtopäätöksen että nämä kirjautumisyritykset ovat automatisoituja. Uskon että nämä käyttäjät kuten `user, jason, tomcat, guest` ovat yleisiä standari käyttäjiä ja näitä yritetään murtaa.
- Sama IP osoite yrittää monella portilla kirjautua järjestelmään sisään.
- `/var/log/apache2/access.log` löysin oikeita kirjautumisia minun testisivulle:

                OMAN PUHELIMENI IP - - [08/Feb/2023:17:25:48 +0000] "GET /favicon.ico HTTP/1.1" 404 436 "http://143.42.57.220/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64)                   AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36"
                
- Ylläoleva esimerkki on minun surffailuni omalla puhelimella.
