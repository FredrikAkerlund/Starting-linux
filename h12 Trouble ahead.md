## Vianselvitystä

Lähtötilanne:

Olen asentanut virtuaalikoneelle Djangon tuotantopalvelimelle joka toimii Apache2 webbipalvelimella.

Django palvelimella ei ole appeja luotu. Joten käyttäjälle on vain näkyvissä `404 not found` kun yrittää päästä palvelimelle `localhost`.

<img width="473" alt="image" src="https://user-images.githubusercontent.com/122887178/222955496-28c53d5b-c751-444c-ba7e-f4bfae40f17a.png">


Asentaessa django tuotantopalvelinta minulla oli ongelmia että mod_wsgi moduli ei toiminut ja apache ei ollu yhteyttä Django palvelimelle. Tässä tehtävässä aloitan kirjoittamalla selvityksen mistä ongelma  johtui ja miten ratkaisin ongelman.

### D) Kirjoitusvirhe apache2 konfiguraatio tiedostossa.

Alkutilanne:

Virtuaalikoneelle on asenettu Apache2 webbipalvelin sekä Django kehityspalvelin. Olen myös asentanut WSGI moduulin.

Alla on localhost conf tiedosto:

      Define TDIR /home/fredrik/publicwsgi/puttes
      #Directory copied from another terminal
      Define TWSGI /home/fredrik/publicwsgi/puttes/puttes/wsgy.py
      #WSGY.py file location
      Define TUSER putte
      #Our defined non SUDO user
      Define TVENV /home/fredrik/publicwsgi/env/lib/python3.9/site-packages
      #File location of virtualenv site package

      <VirtualHost *:80>
              Alias /static/ ${TDIR}/static/
              <Directory ${TDIR}/static/>
                      Require all granted
              </Directory>

              WSGIDaemonProcess ${TUSER} user=${TUSER} group=${TUSER} threads=5 python-path="${TDIR}:${TVENV}"
              WSGIScriptAlias / ${TWSGI}
              <Directory ${TDIR}>
                   WSGIProcessGroup ${TUSER}
                   WSGIApplicationGroup %{GLOBAL}
                   WSGIScriptReloading On
                   <Files wsgi.py>
                      Require all granted
                   </Files>
              </Directory>

      </VirtualHost>

      Undefine TDIR
      Undefine TWSGI
      Undefine TUSER
      Undefine TVENV
      

Näillä asetuksilla pääsen luomaani /static/ sivulle

<img width="336" alt="image" src="https://user-images.githubusercontent.com/122887178/222955883-ccb269ca-71a2-43b9-82bf-e5672af9e837.png">

Mutta `localhost` antoi seuraavan vikakoodin:

            (env) 12:47:33 fredrik@hiekkis:/etc/apache2/sites-available$ curl localhost
            <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
            <html><head>
            <title>403 Forbidden</title>
            </head><body>
            <h1>Forbidden</h1>
            <p>You don't have permission to access this resource.</p>
            <hr>
            <address>Apache/2.4.54 (Debian) Server at localhost Port 80</address>
            </body></html>
Asentaessa tuotantopalvelinta minulla oli kuitenkin kehityspalvelin päällä joten kun kun käytin komentoa `curl localhost:8000` minulle tuli vikakoodi

            404 site not found
            
Tässä vaiheessa ihmettelin että mikä juttu mutta oletin että debuggaus on pois päältä ja että tuotantopalvelin on käyttökunnossa.

Aloin kaivamaan vikaa apache2 error.log tiedostosta.

Error.log tiedosto antoi seuravaan virhekoodin:

            [Sun Mar 05 12:48:56.789191 2023] [authz_core:error] [pid 2348:tid 140410547394304] [client ::1:60458] AH01630: client denied by server configuration: /home/fredrik/publicwsgi/puttes/puttes/wsgy.py
            
Virhelokissä selviää että käyttäjällä ei ole oikeuksia päästä käsiksi tiedostoon wsgy.py. Localhost yrittää portilla :60458 päästä käsiksi tiedostoon joka ei ole olemassa. Jonka takia palvelin estää pääsyn tähän olemattomaan tiedostoon. PID= Process id. TID= Thread ID

TÄmä kertoi että virhe oli wsgi moduulissa.

Tarkastin tarkkaan apache konfiguraatio tiedoston ja varmistin että olin kopioinut oikeat tiedostopolut käyttäen `pwd` komentoa ja kopioimalla sen suoraan toisesta terminaalista välttäen kirjoitusvirheitä.

Kuitenkin en pysty kopioimaan `wsgi.py`tiedoston sijaintia suoraan niin kirjoitin käsin hakemistopolun loppuun tiedosto nimen.

Ensi korjaukseni oli muuttaa /home/fredrik/publicwsgi/puttes/puttes hakemiston oikeuksia `sudo chmod 755 -R /home/fredrik/publicwsgi/puttes/puttes` mutta tämä ei auttanut.

Pienen tauon jälkeen ja tuoreilla silmillä huomasin että wsgi modulin sijainti oli kirjoitettu väärin konfiguraatio tiedostoon.

Virhe löytyi konfiguraatio tiedostosta:

            Define TWSGI /home/fredrik/publicwsgi/puttes/puttes/wsgy.py
            
            Tiedoston sijainnin lopussa pitäisi olla wsgi.py!!!!
            
Potkaisin apache demonia ja nyt wsgi moduuli oli käytössä ja tuotantopalvelin oli oikeasti käytössä.

<img width="449" alt="image" src="https://user-images.githubusercontent.com/122887178/222956426-dda8a2bb-93df-4781-a7cf-02d6964ed2ab.png">

Virheestä opin sen että omien kirjoitusvirheiden löytäminen on erittäin haastavaa. Ja sen että error.log on ystävä ja sieltä vastaukset yleensä löytyvät.

### C) Väärät oikeudet projektikansiolla

Muutan projektikansioni oikeudet siten että vain minun käyttäjä joka on luonut projektikansion saa lukea, kirjoittaa tai suorittaa sen.

            chmod 700 -R /home/fredrik/publicwsgi/puttes
            
Kokeilen päästä apache palvelimelle. Tässä vaiheessa oletan että sivu näyttää `404 not found`

<img width="455" alt="image" src="https://user-images.githubusercontent.com/122887178/222957034-03eae915-8867-41d6-a98d-1aea1c9da771.png">

Aloitan tarkastamalla apache2 error lokit.

            [Sun Mar 05 13:11:16.141834 2023] [core:error] [pid 2616:tid 140368331994880] (13)Permission denied: [client 127.0.0.1:56704] AH00035: access to / denied (filesystem path '/home/fredrik/publicwsgi/puttes/puttes') because search permissions are missing on a component of the path

Error lokia kun tulkitsen näen että clientti localhost yrittää päästä käsiksi tiedostopolkuu /home/fredrik/publicwsgi/puttes/puttes portilla :56704.
Kuitenkin palvelin estää pääsyn kyseiseen tiedospolkuun.

Korjaan tämän ongelman muutamalla oikeudet kansioon. Haluan että tiedostopolun luojalla on oikeus read,write and excecute. Kaikki muut käyttäjät pitäisi saada read and execute.

            13:15:27 fredrik@hiekkis:~/publicwsgi$ sudo chmod -R 755 /home/fredrik/publicwsgi/puttes
Kokeilen toimiiko apache palvelin

      13:25:00 fredrik@hiekkis:~/publicwsgi$ curl localhost

      <!doctype html>
      <html lang="en">
      <head>
        <title>Not Found</title>
      </head>
      <body>
        <h1>Not Found</h1><p>The requested resource was not found on this server.</p>
      </body>
      </html>
      
Ongelma näyttää ratkenneelta.

### E) Apachen wsgi moduli puuttuu

ALoitan luomalla ongelman poistamalla wsgi modulin.

            13:30:57 fredrik@hiekkis:~/publicwsgi$ sudo apt purge libapache2-mod-wsgi-py3
            
Kokeilen `curl localhost` ja katson mitä virhetietoja saan aikaiseksi.

<img width="905" alt="image" src="https://user-images.githubusercontent.com/122887178/222965958-2c1087ec-afdc-4d97-b107-fbaa27d2e82c.png">

Lähden tutkimaan apachen virhelokeja. 
      
            sudo tail -F /var/log/apache2/access.log /var/log/apache2/error.log /var/log/apache2/other_vhost.log

Apachen virhelokeissa ei kuitenkaan ole mitään virheilmoituksia. 

Seuraavaksi kokeilen `/sbin/apache2ctl configtest`. Saan seuraavan tuloksen.

            AH00526: Syntax error on line 16 of /etc/apache2/sites-enabled/puttes.conf:
            Invalid command 'WSGIDaemonProcess', perhaps misspelled or defined by a module not included in the server configuration
            Action 'configtest' failed.
            The Apache error log may have more information.
            
            
Etsin myös processeista onko wsgi processi demoni olemassa.

            16:25:58 fredrik@hiekkis:~/publicwsgi$ ps aux |grep 'wsgi'
            fredrik     1291  0.2  1.1 323120 44208 ?        Sl   16:13   0:01 xfce4-terminal --geometry=92x24 --display :0.0 --role=xfce4-terminal-1677694723-2148409655 --show-menubar --show-borders --hide-toolbar --zoom=5 --active-tab --working-directory /home/fredrik/publicwsgi --sm-client-id 2e1bcb131-6d53-466a-bc0f-e426a2f8dc8b
            fredrik     1796  0.0  0.0   8908   704 pts/0    S+   16:26   0:00 grep wsgi

Tästä näen että WSGI demonia ei ole olemassa.

Asennan wsgi modulin uudestaan

            sudo apt install libapache2-mod-wsgi-py3
            
Käynnistän apache2 demonin uudestaan ja kokeilen `curl localhost`

            16:35:51 fredrik@hiekkis:~/publicwsgi$ sudo systemctl restart apache2
            16:35:59 fredrik@hiekkis:~/publicwsgi$ curl localhost

            <!doctype html>
            <html lang="en">
            <head>
              <title>Not Found</title>
            </head>
            <body>
              <h1>Not Found</h1><p>The requested resource was not found on this server.</p>
            </body>
            </html>
            
Apache palvelin näyttää toimivan. Kokeilen myös etsiä wsgi demonia.

            
            16:48:49 fredrik@hiekkis:~/publicwsgi/puttes/puttes$ ps aux|grep -i  wsgi
            fredrik     1291  0.1  1.1 323120 44208 ?        Sl   16:13   0:03 xfce4-terminal --geometry=92x24 --display :0.0 --role=xfce4-terminal-1677694723-2148409655 --show-menubar --show-borders --hide-toolbar --zoom=5 --active-tab --working-directory /home/fredrik/publicwsgi --sm-client-id 2e1bcb131-6d53-466a-bc0f-e426a2f8dc8b
            fredrik     2611  0.0  0.0   8908   640 pts/0    S+   16:49   0:00 grep -i wsgi
            
Tämä antoi kuitenkin saman virheen. En tiedä miten etsin wsgi demonin.


### F) Väärät domain nimet allowed host kohdassa

Aloitan poistamalla allowed host django projektini settings.py tiedostosta.

            # SECURITY WARNING: don't run with debug turned on in production!
            DEBUG = False

            ALLOWED_HOSTS = []
Kun kokeilen päästä selaimella apachepalvelimelle saan seuraavan virheen

<img width="371" alt="image" src="https://user-images.githubusercontent.com/122887178/222968051-b5050657-9c06-4b8c-92a8-2fe44f0bc705.png">

Kun kokeilen curl local host saan seuraavaan virheen.

            :54:02 fredrik@hiekkis:~/publicwsgi/puttes/puttes$ curl locahost
            curl: (6) Could not resolve host: locahost
Etsin taas vihjeitä apache2 virhelokeista

            sudo tail -F /var/log/apache2/access.log /var/log/apache2/error.log /var/log/apache2/other_vhost.log
            
En kuitenkaan löydä mitään uusia virheitä. Seuraavaksi kokeilen configtestejä

            16:57:12 fredrik@hiekkis:~/publicwsgi/puttes/puttes$ /sbin/apache2ctl configtest
            AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
            Syntax OK

Tämäkin näyttää että kaikki on kunnossa

Seuraavaksi tutkiselisin tiedostoa `settings.py`. Nyt koska tiedän missä virhe on niin on helppo korjata se. En tiedä miten muuten löytäisin tämän virheen.

Seuraavaksi korjaan virheen ja homma pitäisi toimia taas.

Lisäksi käytän `touch wsgi.py`

Korjattuani virheen saan seuraavan virheen

<img width="1101" alt="image" src="https://user-images.githubusercontent.com/122887178/222968816-b3620b85-b952-4437-97d2-dd3b84691d55.png">

Potkaisen apache2 demonia

Tämäkään ei auta.

Tajuan että kirjoitin `settings.py` allowed host ilman `'localhost'`. Korjaan tämän ja homma toimii taas.

<img width="460" alt="image" src="https://user-images.githubusercontent.com/122887178/222968932-dd9bd024-3c58-4e9a-b20b-81be8ed037ed.png">


Tätä virhettä en välttämättä olisi löytänyt ilman että olisin tiennyt mikä sen aiheutti.


### A) Kirjoitusvirhe python tiedostossa

Edelisessä aiheutin itse juurikin tämän saman virheen. Teen virheen uudestaan ja katson mitä lokeista saa selvillä.

Poistan `Settings.py` tiedostosta allowedhost kohdassa `' '` merkit ja katson mitä virheitä saan.

            17:16:28 fredrik@hiekkis:~/publicwsgi/puttes/puttes$ curl localhost
            <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
            <html><head>
            <title>500 Internal Server Error</title>
            </head><body>
            <h1>Internal Server Error</h1>
            <p>The server encountered an internal error or
            misconfiguration and was unable to complete
            your request.</p>
            <p>Please contact the server administrator at 
             [no address given] to inform them of the time this error occurred,
             and the actions you performed just before this error.</p>
            <p>More information about this error may be available
            in the server error log.</p>
            <hr>
            <address>Apache/2.4.54 (Debian) Server at localhost Port 80</address>
            </body></html>


Seuraavat askeleet on tarkastaa error lokit jälleen kerran:

            [Sun Mar 05 17:16:33.113747 2023] [wsgi:error] [pid 3135:tid 140048697231104] [remote ::1:48896]   File "/home/fredrik/publicwsgi/puttes/puttes/settings.py", line 28, in <module>
            [Sun Mar 05 17:16:33.113751 2023] [wsgi:error] [pid 3135:tid 140048697231104] [remote ::1:48896]     ALLOWED_HOSTS = [localhost]
            [Sun Mar 05 17:16:33.113785 2023] [wsgi:error] [pid 3135:tid 140048697231104] [remote ::1:48896] NameError: name 'localhost' is not defined

Tämä antaa selkeän vastaukset ja vika löyty välittömästi.

Korjaan virheen ja kokeilen toimiiko apache2 webbipalvelin.

            17:19:43 fredrik@hiekkis:~/publicwsgi/puttes/puttes$ curl localhost

            <!doctype html>
            <html lang="en">
            <head>
              <title>Not Found</title>
            </head>
            <body>
              <h1>Not Found</h1><p>The requested resource was not found on this server.</p>
            </body>
            </html>
            
Ratkaisu oli helppo ja error loki antoi suoraan vastaukset miten korjaan ongelman.

### B) Django-projektikansio väärässä paikassa





     
            

