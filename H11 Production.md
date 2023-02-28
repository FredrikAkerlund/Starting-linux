## H11 Production

Viikko tehtävänä on tehdä Django tuotantoasennus. Tehtävään käytän hyödeykseni Tero Karvisen artikkelia. <br>
Lähde: https://terokarvinen.com/2022/deploy-django/

Tuotanto palvelimella käytetään modulia `mod.wsgi`

Django palvelimen tuotantoasennukseen tarvitaan:
- Linux tietokone, Itsellä on virtualhostin avulla asenettu virtuaalikone
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

        



      





        

