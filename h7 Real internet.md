## h7 Real internet (tm)

### Tero Karvinen 2012: First steps on a virtual private server (VPS)


Tämä artikkeli kuvaa ensimmäisiä askeleita uuden virtuaalipalvelimen asennuksessa DigitalOceanilla:

Uuden palvelimen luominen
- Yhteyden luominen palvelimeen SSH:lla
- Tarvittavien ohjelmien asentaminen
- Vinkkejä turvallisuuden parantamiseen, kuten esimerkiksi:
- Tietoturvallisuuden parantaminen muuttamalla oletus SSH-porttia
- Sallittujen IP-osoitteiden määrittely
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
             
### Murtoyritysten havaitseminen
