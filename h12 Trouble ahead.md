## Vianselvitystä

Lähtötilanne:

Olen asentanut virtuaalikoneelle Django tuotantopalvelimelle joka toimii Apache2 webbipalvelimella.

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
            
Tässä vaiheessa ihmettelin että mikä juttu mutta ja oletin että debuggaus on pois päältä ja että tuotantopalvelin on käyttökunnossa.

Aloin kaivamaan vikaa apache2 error.log tiedostosta.

Error.log tiedosto antoi seuravaan virhekoodin:

            [Sun Mar 05 12:48:56.789191 2023] [authz_core:error] [pid 2348:tid 140410547394304] [client ::1:60458] AH01630: client denied by server configuration: /home/fredrik/publicwsgi/puttes/puttes/wsgy.py
            
Virhelokissä selviää että käyttäjällä ei ole oikeuksia päästä käsiksi tiedostoon wsgy.py. Localhost yrittää portilla :60458 päästä käsiksi tiedostoon joka ei ole olemassa. PID= Process id. TID= Thread ID

TÄmä kertoi että virhe oli wsgi moduulissa.

Tarkastin tarkkaan apache konfiguraatio tiedoston ja varmistin että olin kopioinut oikeat tiedostopolut käyttäen `pwd` komentoa ja kopioimalla suoraan toisesta terminaalista.

Kuitenkin en pysty kopioimaan `wsgi.py`tiedosto sijaintia suoraan niin kirjoitin käsin hakemistopolun loppuun tiedosto nimen.

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
            
            

