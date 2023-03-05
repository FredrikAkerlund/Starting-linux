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
      

