## h10 DJ ango
Aloitin tehtävän 07:55

Käytän tehtävässä Tero Karvisen ohjeita. Lähde : https://terokarvinen.com/2022/django-instant-crm-tutorial/

### Django asennus

- Aloitan päivittämällä repot

        sudo apt update
- Asennan kehitysympäristön

         sudo apt install -y virtualenv
         
- Luon käyttämän kotihakemistoon uuden kansion nimeltä `dj_ango`
- Tämän kansion sisälle luon virtuaali kehitysympäristöni.


        virtualenv --system-site-packages -p python3 env
        
- Tarkistan että `env` niminen kansio luotiin hakmistoon `dj_ango`

        
                fredrik@hiekkis:~$ cd dj_ango/
                fredrik@hiekkis:~/dj_ango$ ls
                env
- Aktivoin virtuaaliympäristöni.

                fredrik@hiekkis:~/dj_ango$ source env/bin/activate
                (env) fredrik@hiekkis:~/dj_ango$ 
- Tässä vaiheessa huomasin että minun pitää olla hakemistossa minkä sisältä löytyy `env` kansio. Jos olen väärässä kansiossa saan virheilmoituksen:

                fredrik@hiekkis:~$ source env/bin/activate
                bash: env/bin/activate: No such file or directory
                
- `Deactivate` komenolla pääsen pois `(env)` tilasta.
- Tarkastan mitä `pip` käytämme (pip=python package manager)

                (env) fredrik@hiekkis:~/dj_ango$ which pip
                /home/fredrik/dj_ango/env/bin/pip
                
- Luon tiedoston `requirements.txt` jonne kirjoitan "django"

                (env) fredrik@hiekkis:~/dj_ango$ micro requirements.txt
                (env) fredrik@hiekkis:~/dj_ango$ cat requirements.txt 
                "django"
                
- Yritän asentaan Djangon. Mutta saan seuraavan virheilmoituksen:

                (env) fredrik@hiekkis:~/dj_ango$ pip install -r requirements.txt 
                ERROR: Invalid requirement: '"django"' (from line 1 of requirements.txt)
                
- Poistan `""` merkit `requirements.txt` tiedostosta ja kokeilen uudestaan.

                (env) fredrik@hiekkis:~/dj_ango$ micro requirements.txt 
                (env) fredrik@hiekkis:~/dj_ango$ pip install -r requirements.txt 
                Collecting django
                  Downloading Django-4.1.7-py3-none-any.whl (8.1 MB)
                     |████████████████████████████████| 8.1 MB 646 kB/s 
                Collecting asgiref<4,>=3.5.2
                  Downloading asgiref-3.6.0-py3-none-any.whl (23 kB)
                Collecting sqlparse>=0.2.2
                  Downloading sqlparse-0.4.3-py3-none-any.whl (42 kB)
                     |████████████████████████████████| 42 kB 1.7 MB/s 
                Installing collected packages: sqlparse, asgiref, django
                Successfully installed asgiref-3.6.0 django-4.1.7 sqlparse-0.4.3

- Tarkastan djangon resurssisivuilta mikä on uusin django julkaisu.
- Lähde: https://docs.djangoproject.com/en/4.1/releases/
- Uusin versio on 4.1.7
- Tarkastan että asensin uusimman version

                (env) fredrik@hiekkis:~/dj_ango$ django-admin --version
                4.1.7
                
- Kello on 08:16 kun django oli asennettu

### DJangon käyttöönotto

- Aloitan uuden django projektin nimellä fredrikakerlund

                (env) fredrik@hiekkis:~/dj_ango$ django-admin startproject fredrikakerlund
                (env) fredrik@hiekkis:~/dj_ango$ ls
                env  fredrikakerlund  requirements.txt
- Näen että `dj_ango` hakemistoon ilmeistyi fredrikakerlund kansio joka on uusi projektini
- Käynnistän palvelimen

                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ ./manage.py runserver
                Watching for file changes with StatReloader
                Performing system checks...

                System check identified no issues (0 silenced).

                You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
                Run 'python manage.py migrate' to apply them.
                February 21, 2023 - 06:29:01
                Django version 4.1.7, using settings 'fredrikakerlund.settings'
                Starting development server at http://127.0.0.1:8000/
                Quit the server with CONTROL-C.
- Näyttä siltä että palvelin toimii. Kokeilen selaimella `localhost:8000` että palvelin toimii.

![image](https://user-images.githubusercontent.com/122887178/220265356-fbf8c8de-06c1-464a-83a4-556833cdd14f.png)

- Yritän päästä käsiksi admin sivulle `localhost:8000/admin`.

![image](https://user-images.githubusercontent.com/122887178/220265757-5e76ff89-7c31-4b4c-8a55-90bda1495f05.png)

- Päivitän tietokannan ja luon uuden `superuser` käyttäjän.

                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ ./manage.py makemigrations
                No changes detected
                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ ./manage.py migrate
                Operations to perform:
                  Apply all migrations: admin, auth, contenttypes, sessions

- Salasanan luontiin käytän juuri asentamaani salasanageneraattoria. 


               (env) fredrik@hiekkis:~/dj_ango$ cd fredrikakerlund/
                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ ./manage.py createsuperuser
                Username (leave blank to use 'fredrik'): 
                Email address: 
                Password: 
                Password (again): 
                Superuser created successfully.
- Kokeilen kirjautua Admin login pagelle tällä käyttäjällä.
- Tunnuksilla ei pysty kirjautumaan Djangon admin kotisivulle.
- Päivitän tietokannat ja katson muuttuuko mitään.

                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ ./manage.py migrate
                Operations to perform:
                  Apply all migrations: admin, auth, contenttypes, sessions
                Running migrations:
                  No migrations to apply.
                  
- Tajuan että käyttäjä on fredrikakerlund koska jätin username kohdan tyhjäki kun loin superuser käyttäjän:

                  (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ ./manage.py createsuperuser
                Username (leave blank to use 'fredrik'): 
                
- Admin sivulta luon 2 uutta käyttäjää `Erkki` ja `sirkka`.

![image](https://user-images.githubusercontent.com/122887178/220269863-68a83c61-3d0f-4452-8150-2708b89ba7a4.png)

### Tietokantojen luominen

- Luon djangoon uuden appin nimeltä `products`.

                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ ./manage.py startapp products
                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ ls
                db.sqlite3  fredrikakerlund  manage.py  products
- Kuten näkyy projektiin `fredrikakerlund` luotiin kansio nimeltä products. Se on meidän juuri luomamme appi
- Lisään tämän appin `settings.py` tiedostoon.

                INSTALLED_APPS = [
                    'django.contrib.admin',
                    'django.contrib.auth',
                    'django.contrib.contenttypes',
                    'django.contrib.sessions',
                    'django.contrib.messages',
                    'django.contrib.staticfiles',
                    'products', ## Tämän lisäsin
                ]

- Lisään mallin tiedostoon `products`

        (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund/products$ cat models.py 
        from django.db import models

        class Products(models.Model):
            name =models.CharField(max_length=300)
            
- Päivitän tietokannat

                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ ./manage.py makemigrations
                Migrations for 'products':
                  products/migrations/0001_initial.py
                    - Create model Products
                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ ./manage.py migrate
                Operations to perform:
                  Apply all migrations: admin, auth, contenttypes, products, sessions
                Running migrations:
                  Applying products.0001_initial... OK
- Nähdäkseni uuden tietkannan `products` admin sivulla, ne pitää rekisteröidä
- Lisään tiedostoon `admin.py` rekisteröinnin.

                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund/products$ cat admin.py 
                from django.contrib import admin
                from.import models
                admin.site.register(models.Product)
                # Register your models here.

- Päivitän selaimen ja vastaan tulee seuraava ongelma:
![image](https://user-images.githubusercontent.com/122887178/220274910-fe43ccf7-dffe-46b6-a1a5-766bf3224573.png)
- Tarkastan terminaalin missä Django palvelin pyörii. Terminaalista selviää seuraava ongelma:

                  File "/home/fredrik/dj_ango/fredrikakerlund/products/admin.py", line 3, in <module>
                    admin.site.register(models.Product)
                AttributeError: module 'products.models' has no attribute 'Product'
                
- Virhe ilmoituksesta huomaan että tein kirjoitusvirheen luokka rekisteröinnissä `admin.py` tiedostossa.
- Korjaan tämän

                micro admin.py
                
                from django.contrib import admin
                from.import models
                admin.site.register(models.Products)# Tämän korjasin
                # Register your models here.
- Päivitän selaimen ja huomaan että tietokanta appi `Products` on ilmestynyt sinne mutta tietokanta on nimetty `Productss`.

![image](https://user-images.githubusercontent.com/122887178/220277021-e2b0e8d6-c2c0-46b7-b7e9-076eadbbd3c0.png)

- Seuraavaksi lähden korjaamaan tätä ongelmaa.
- Nimeän appin hakemiston uudestaan

                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ mv products/ product/
                
- Pävitän `apps.py` tiedoston täsmäämään tekemääni muutoksiin

                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund/product$ cat apps.py 
                from django.apps import AppConfig


                class ProductsConfig(AppConfig):
                    default_auto_field = 'django.db.models.BigAutoField'
                    name = 'product'
                    
- Päivitän projektin `settings.py` tiedostoon nimen.

                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund/fredrikakerlund$ micro settings.py
                        INSTALLED_APPS = [
                            'django.contrib.admin',
                            'django.contrib.auth',
                            'django.contrib.contenttypes',
                            'django.contrib.sessions',
                            'django.contrib.messages',
                            'django.contrib.staticfiles',
                            'product',
                             ]
- Päivitän tietokannan ja kokeilen tuliko muutoksia admin sivulle.
                
                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ ./manage.py makemigrations
                No changes detected
                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund$ ./manage.py migrate
                Operations to perform:
                  Apply all migrations: admin, auth, contenttypes, product, sessions
                Running migrations:
                  Applying product.0001_initial... OK
                  
- Päivitän selaimen ja kokeilen uudestaan
![image](https://user-images.githubusercontent.com/122887178/220281226-a6ac7280-d490-4b13-87b3-89a9ddc0e686.png)
- Appin nimi on muuttunu mutta tietokanta on vielä väärin.
- Oletan että tämä johtuu `models.py` tiedoston väärin asettelusta.
- Poistun tiedostosta `Products` ja korvasin sen `Products`
                
                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund/product$ cat models.py 
                from django.db import models
                #Class should always be in singular
                class Product(models.Model):
                    name =models.CharField(max_length=300)
- Korjasin myös admin.py tiedostoon rekisteröinnin admin sivulle täsmäämään oikeata luokkaa.

                (env) fredrik@hiekkis:~/dj_ango/fredrikakerlund/product$ cat admin.py 
                from django.contrib import admin
                from.import models
                admin.site.register(models.Product)# Tämän korjasin
                # Register your models here.
                
- Päivitän selaimen ja kokeilen uudestaan:

![image](https://user-images.githubusercontent.com/122887178/220283023-d1094bed-751b-473c-811a-176e4a3023b9.png)
- Nyt näyttää oikealta.
- Kokeilen päästä muokkaamaan tietokantaa `Products`
![image](https://user-images.githubusercontent.com/122887178/220283777-112fa448-c959-43d9-b3e8-be8b1b2cd03a.png)

- 


                
                

                
