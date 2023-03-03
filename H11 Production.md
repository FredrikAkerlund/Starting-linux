## H11 Production

Viikko tehtävänä on tehdä Django tuotantoasennus. Tehtävään käytän hyödeykseni Tero Karvisen artikkelia. <br>
Lähde: https://terokarvinen.com/2022/deploy-django/

Tuotanto palvelimella käytetään modulia `mod.wsgi`

Django palvelimen tuotantoasennukseen tarvitaan:
- Linux tietokone, Itsellä on virtualhostin avulla asennettu virtuaalikone
- Apache2 webbipalvelin asenettuna virtuaalikoneelle
Jos haluaa asentaa palvelimen julkiseen internettiin tarvitaan myös Virtual Private server. Itsellä on käytössä Linoden kautta vuokraamaa palvelin.


Aloitan asennuksen päivittämällä kirjastoni

          sudo apt-get update
          sudo apt-get upgrade
          

Minulla on asenettuna apache2 palvelin ja käytössä muutama kotisivun conffaus. Otan nämä sivut pois käytöstä ja luon uuden.

      2008  sudo a2dissite bar.example.com.conf foo.example.com.conf localhost.conf 
Tarkistan että apache2 palvelin näyttää testisivuni

<img width="277" alt="image" src="https://user-images.githubusercontent.com/122887178/221925990-cd638a92-12e5-43e3-aab1-347d05420389.png">


Tarkastan tiedoston `/var/www/html/index.html` että Apachepalvelin näyttää Apache testisivun

        19:08:49 fredrik@hiekkis:/var/www/html$ cat index.html 
        Apache2 testsite

Seuraavaksi luon statisen sivun Apache2 weppipalvimelle

        19:13:07 fredrik@hiekkis:~$ mkdir -p publicwsgi/puttes/static
        19:13:52 fredrik@hiekkis:~/publicwsgi/puttes/static$ micro index.html
        19:14:43 fredrik@hiekkis:~/publicwsgi/puttes/static$ cat index.html 
        Welcome to Putteswebsite
        powered by Django
        
Luon .conf tiedoston `/etc/apache2/sites-available`. Kopion toisen .conf tiedoston ja mnuokkaan sitä että täsmää uuden statisen hakemistoon

        19:16:50 fredrik@hiekkis:/etc/apache2/sites-available$ sudo cp localhost.conf puttes.conf
        19:18:29 fredrik@hiekkis:/etc/apache2/sites-available$ micro puttes.conf
        
<img width="968" alt="image" src="https://user-images.githubusercontent.com/122887178/221929319-33667f6c-480f-41c0-8bd1-cea1aa970c24.png">

Enabloin juuri tekemäni statisen kotisivun

        19:22:08 fredrik@hiekkis:/etc/apache2/sites-available$ sudo a2ensite puttes.conf 
        
Tarkastan että .conf tiedosto on käyttö kelpoinen

        19:23:06 fredrik@hiekkis:/etc/apache2/sites-available$ /sbin/apache2ctl configtest
        AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to            suppress this message
        Syntax OK
        
Käynnistän Apache2 demonin ja tarkastan että kotisivu on muuttunut

      19:23:52 fredrik@hiekkis:/etc/apache2/sites-available$ sudo systemctl restart apache2
      19:27:08 fredrik@hiekkis:/etc/apache2/sites-available$ curl localhost/static/
      Welcome to Putteswebsite
      powered by Django
      
#### Yhteenveto 

Olen nyt luonut statisen HTML sivun apache2 weppipalvelimelle. En ole vielä asentanut DJango ympäristöä tai luonnut mitään tietokantoja. Seuraavissa vaiheissa luon `mod_wsgi` modulin jonka avulla apache2 palvelin keskustelee Django ympäristön kanssa. TÄmä modulin asennan virtuaaliympäristön sisälle `virtualenv`.

Kun asennan uutta virtuaaliympäristöä varmistan että minulla ei ole toisia luotu tietokoneella. Tiedän että minulla on joten poistan nämä hakemistot

        19:33:06 fredrik@hiekkis:~$ rm -r dj_ango/
Tiedän että hakemistoss `home/fredrik/dj_ango` on olemassa django palvelin asenettuna kehityskäyttöön. `rm -r` komento poistaa kansion kokonaisuudessaan.

Asennan uuden virtuaaliympäristön hakemistoon `publicwsgi/`

        
        19:34:52 fredrik@hiekkis:~$ cd publicwsgi/
        19:35:36 fredrik@hiekkis:~/publicwsgi$ ls
        puttes
        19:35:37 fredrik@hiekkis:~/publicwsgi$ virtualenv -p python3 --system-site-packages env
        created virtual environment CPython3.9.2.final.0-64 in 231ms
          creator CPython3Posix(dest=/home/fredrik/publicwsgi/env, clear=False, no_vcs_ignore=False, global=True)
          seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/fredrik/.local/share/virtualenv)
            added seed packages: pip==20.3.4, pkg_resources==0.0.0, setuptools==44.1.1, wheel==0.34.2
          activators BashActivator,CShellActivator,FishActivator,PowerShellActivator,PythonActivator,XonshActivator

Aktivoin virtuaaliympäristön ja varmistan että Pythonin paketinhallinta ajetaan virtuaaliympäristöstä

        19:36:11 fredrik@hiekkis:~/publicwsgi$ source env/bin/activate
        (env) 19:37:22 fredrik@hiekkis:~/publicwsgi$ which pip
        /home/fredrik/publicwsgi/env/bin/pip
        (env) 19:37:26 fredrik@hiekkis:~/publicwsgi$ 
        
Asennan djangon uuteen virtuaaliympäristöön ja tarkastan versionumeron

        (env) 19:38:50 fredrik@hiekkis:~/publicwsgi$ micro requirements.txt
        # tiedostoon kirjoitan "django"
        
        (env) 19:39:07 fredrik@hiekkis:~/publicwsgi$ pip 
        env/              puttes/           requirements.txt  
        (env) 19:39:07 fredrik@hiekkis:~/publicwsgi$ pip 
        env/              puttes/           requirements.txt  
        (env) 19:39:07 fredrik@hiekkis:~/publicwsgi$ pip install -r requirements.txt 
        Collecting django
          Using cached Django-4.1.7-py3-none-any.whl (8.1 MB)
        Collecting sqlparse>=0.2.2
          Using cached sqlparse-0.4.3-py3-none-any.whl (42 kB)
        Collecting asgiref<4,>=3.5.2
          Using cached asgiref-3.6.0-py3-none-any.whl (23 kB)
        Installing collected packages: sqlparse, asgiref, django
        Successfully installed asgiref-3.6.0 django-4.1.7 sqlparse-0.4.3
        (env) 19:39:32 fredrik@hiekkis:~/publicwsgi$ django-admin --version
        4.1.7

### Yhteenveto

Poistin vanhat django projectit käyttäjän kotihakemistosta. Asensin täysin uuden virtuaaliympäristön. Tähän ympäristöön asensin djangon.

        
Seuraavaksi aloitan uuden projektin nimeltä `puttes`

          (env) 19:39:43 fredrik@hiekkis:~/publicwsgi$ django-admin startproject puttes
          CommandError: '/home/fredrik/publicwsgi/puttes' already exists
          
Jotta pääsen eroon virhekoodista poistan hakemiston `puttes` ja aloitan projektin uudestaan

          
          (env) 19:45:14 fredrik@hiekkis:~/publicwsgi$ django-admin startproject puttes
          
Jotta saan statisen sivun näkyviin apache2 palvelimelle luon uuden `index.html` tiedoston.

          (env) 19:45:34 fredrik@hiekkis:~/publicwsgi/puttes$ mkdir -p static
          (env) 19:46:22 fredrik@hiekkis:~/publicwsgi/puttes$ ls
          manage.py  puttes  static
          (env) 19:46:23 fredrik@hiekkis:~/publicwsgi/puttes$ cd static/
          (env) 19:46:29 fredrik@hiekkis:~/publicwsgi/puttes/static$ ls
          (env) 19:46:30 fredrik@hiekkis:~/publicwsgi/puttes/static$ micro index.html
          
<img width="324" alt="image" src="https://user-images.githubusercontent.com/122887178/221935658-759c547c-9d3e-48da-8a73-24dcfd66e1e6.png">

### Yhteenveto

Loin uuden django projektin nimellä `puttes`. Loin uuden statisen sivun apach2 webbipalvelimelle placeholderiksi. Jatkan apachen ja djangon yhdistämistä hyvin nukutun yön jälkeen.

Jatkan tehtävää 3.3.

Jatkan tehtävää 3.3 kl 0734

Luon uuden käyttäjän joka ei ole SUDO ryhmässä

          sudo adduser putte
          
Luon django projektille uuden käyttäjän nimellä `putte`

          (env) 07:49:32 fredrik@hiekkis:~/publicwsgi/puttes$ ./manage.py createsuperuser
          Username (leave blank to use 'fredrik'): putte
          Email address: putte@putte.fi
          Password: 
          Password (again): 
          Superuser created successfully.
          
Kokeilen että admin sivu toimii:
- Käynnistän palvelimen

          (env) 07:54:13 fredrik@hiekkis:~/publicwsgi/puttes$ ./manage.py runserver
          
- Kirjaudun äsken luomani käyttäjän kanssa palvelimelle

          <img width="469" alt="image" src="https://user-images.githubusercontent.com/122887178/222643683-2982580e-2a0a-4e2c-afc2-5efd42d41f60.png">


Palvelin toimii ja pystyn kirjautumaan admin sivulle.


Mod_wsgi on moduli joka mahdollistaa apache palvelimen Python ohjelmistojen ajamisen.

Käyttääksen mod_wsgi modulia muutan apache2 sivun .conf tiedostoa. Lähde: https://terokarvinen.com/2022/deploy-django/
          
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
          
Asennan apache wsgi modulin

          08:25:41 fredrik@hiekkis:~$ sudo apt-get -y install libapache2-mod-wsgi-py3
Potkaisen apache2 demonia ja käynnistän sen uudestaan

          08:27:36 fredrik@hiekkis:~$ sudo systemctl restart apache2
Tarkistan syntaxin myös

          AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
          Syntax OK
          
Kokeilen toimiiko apache2 palvelin. Jotain on pielessä lähden etsimään vikaa.

          08:33:26 fredrik@hiekkis:~$ curl localhost
          <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
          <html><head>
          <title>403 Forbidden</title>
          </head><body>
          <h1>Forbidden</h1>
          <p>You don't have permission to access this resource.</p>
          <hr>
          <address>Apache/2.4.54 (Debian) Server at localhost Port 80</address>
          </body></html>
          
          
Kokeilen vianetsintää:

          08:47:34 fredrik@hiekkis:~/publicwsgi/puttes/static$ sudo tail -1 /var/log/apache2/error.log
          [Fri Mar 03 08:43:10.741737 2023] [authz_core:error] [pid 3794:tid 140432477533952] [client ::1:36892] AH01630: client denied by server configuration: /home/fredrik/publicwsgi/puttes/puttes/wsgy.py
          
Kuitenkin url localhost/static/ toimii

<img width="222" alt="image" src="https://user-images.githubusercontent.com/122887178/222652926-24ea4725-bc99-43b1-8467-687e5696c55b.png">


Tämä jäi avoimeksi kysymykseksi. Miksi localhost ei toimi mutta localhost/static/ toimii?? ## Selvitin ongelman myöhemmässä vaiheessa

Jatkan tehtävää kuitenkin.

Käynnistän django palvelimen ja kokeilen selaimella että tämä toimii:

<img width="990" alt="image" src="https://user-images.githubusercontent.com/122887178/222655792-4900c344-6b13-4bb1-afed-841c9ef7c4d5.png">

### Yhteenveto
Moduli wsgi on nyt toiminassa ja django palvelin toimii. ## Todellisuudessa mod_wsgi ei ollut toiminassa

Debug asetuksien muuttaminen

Muutan tiedostoa `settings.py` minun djangopalvelimen apissa.

<img width="487" alt="image" src="https://user-images.githubusercontent.com/122887178/222656487-09d3bf69-3fee-4059-9ae5-b75282d98529.png">

Jotta muutokset tulevat voimaan minun pitää aktivoida `wsgi.py` tiedosto. Samalla käynnistän apache2 demonin uudestaan

          (env) 09:19:08 fredrik@hiekkis:~/publicwsgi/puttes/puttes$ touch wsgi.py 
          (env) 09:19:12 fredrik@hiekkis:~/publicwsgi/puttes/puttes$ sudo systemctl restart apache2
Kokeilen selaimella ja varmistan että debuggaus on pois päältä:

<img width="273" alt="image" src="https://user-images.githubusercontent.com/122887178/222657265-2c25a7bf-41e0-48e8-8ca7-13e3ac865135.png">

Kuitenkin kun käytän `curl localhost` niin näen mitä vikoja on palvelimessa.

          (env) 09:23:01 fredrik@hiekkis:~/publicwsgi/puttes/puttes$ curl localhost
          <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
          <html><head>
          <title>403 Forbidden</title>
          </head><body>
          <h1>Forbidden</h1>
          <p>You don't have permission to access this resource.</p>
          <hr>
          <address>Apache/2.4.54 (Debian) Server at localhost Port 80</address>
          </body></html>
          
Tätä vikaa en ymmärrä.

Lisään `settings.py` tiedostoon 

<img width="614" alt="image" src="https://user-images.githubusercontent.com/122887178/222658829-9116c30f-7fcc-47ff-8d15-ce4a5453e49e.png">

Tämä kuitenkin aiheuttaa paljon ongelmia.

Muokkasin järjestystä ja nyt suostui tekemään muutokset.

Tyylitys Djangon sivuille on nyt enabloitu
<img width="558" alt="image" src="https://user-images.githubusercontent.com/122887178/222674723-6d347201-8d7d-4261-aa0e-8dc326a55d1f.png">


Minulle jäi epäselväksi miksi `localhost` antaa virhekoodin "site forbidden".

### Jatkoa jatkolle. Ongelma ratkesi

Huomasin että `sites-available` tiedostossa oli kirjoitus virhe.

<img width="791" alt="image" src="https://user-images.githubusercontent.com/122887178/222687742-81d7eed9-ddcd-4a31-b22d-0a6cf43f47fc.png">

Define TWSGI /home/fredrik/publicwsgi/puttes/puttes/wsgi.py !!!!!!!!

Kirjoitusvirhe oli hakemistopolun lopussa. Olin vahingossa kirjoittanut /wsgy.py

### Yhteenveto

Sain tehtävän suoritettua loppuun monen virheen kautta.

![image](https://user-images.githubusercontent.com/122887178/222688488-938811d8-0e86-424c-baa0-cb96bb1ed2f5.png)

Palvelin näytti pitkään toimivan mutta kun lähdin tarkastamaan oliko debuggaus pois päältä ihmettelin miksi `localhost` ja `localhost:8000` antoi eri vikakoodin. Avatessani Teron piirtämän mestariteoksen aloin ymmärtämään missä ongelma oli.

Apache2 error loki antoi lopulta vastauksen missä ongelma oli. 






      





        

