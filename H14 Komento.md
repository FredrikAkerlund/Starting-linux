## H14 Komento

Viikon tehtävänä on luoda uusia komentoja linux terminaaliin. Tehtävänä on luoda bashilla ja pythonilla komento jota kaikki käyttäjät voivat käyttää.
Viime viikon tehtävässä innostuin ja tein tämän tehtävän jo.

### Viime viikon tehtävä:

Mitä sain aikaseksi:

<img width="940" alt="image" src="https://user-images.githubusercontent.com/122887178/224506955-ef4f3c35-d7e7-407a-a95d-e210213d0a74.png">

Lähdekoodi:


        import os
        #Tuo tietoja käyttöjärjestelmästä
        import datetime
        #Hae järjestelmästä aika
        user = os.getlogin()
        #Määritä muuttuja "use" kirjautuneen käyttäjän tiedot
        now = datetime.datetime.now()
        #Määritä aika now muuttujaksi
        formatted_date_time = now.strftime("%d.%m.%Y %H:%M")
        # Tee muuttuja joka formatoi ajan mukavaan muotoon (DD:MM:YY, TT:MM
        print(f"Hello {user}")
        #Printtaa hello + muuttuja {user}, Print (f"") mahdollistaa muuttujan lisäämisen sulkujen sisälle
        print(f"Päivämäärä ja kello: {formatted_date_time}")
        #Printtaa päivämäärä ja kello
        print(f"Olet tällä hetkellä kansiossa: {os.getcwd()} ")
        # Printtaa "PWD"
        print("Muista verrytellä ja punnerella työn ohessa. Muuten backend hommat muuttuu hirmu raskaiksi")
        # Printtaa staattinen lauseke
        print("Tässä päivän vitsi:")
        import requests
        import json
        url = 'https://api.chucknorris.io/jokes/random'
        response = requests.get(url)
        #Tämä rivi lähettää HTTP GET pyynnön urlista

        data = json.loads(response.text)
        #Json.loads muuttaa JSON vastauksen python kirjastoksi
        joke = data['value']
        #Value on se alkio missä itse vitsi sijaitsee JSON vastauksessa


        print(joke)
        #Tulostaa itse vitsin terminaaliin
        

## Bashilla tehty komento

Aloitan luomalla uuden tiedoston hakemistoon missä minulla on kaikki scriptini tallessa. Testaan sen välittömästi. Tiedostoon kirjoitan pelkästään `pwd`

        21:17:19 fredrik@hiekkis:~/koodit/myprograms$ micro saluteme.sh
        21:17:35 fredrik@hiekkis:~/koodit/myprograms$ bash saluteme.sh 
        /home/fredrik/koodit/myprograms
        21:18:05 fredrik@hiekkis:~/koodit/myprograms$ ^C

Totean että tämä toimii.

Muokkaan scriptin yksinkertaiseksi laskuriksi:

        #!/bin/bash

        echo Ensimäinen numero
        read x
        echo Toinen numero
        read y
        ((sum=x+y))
        echo Laskutoimituksen tulos on $sum
        
Tulos: (huom muutin tiedoston nimen)

        21:50:50 fredrik@hiekkis:~/koodit/myprograms$ micro calculator.sh 
        21:51:29 fredrik@hiekkis:~/koodit/myprograms$ bash calculator.sh
        Ensimäinen numero
        1
        Toinen numero
        1
        Laskutoimituksen tulos on 2
        
Seuraavaksi teen tiedoston kaikille käytettäväksi komennoksi:
        
        cp: cannot create regular file '/usr/local/bin/calculator.sh': Permission denied
        21:53:48 fredrik@hiekkis:~/koodit/myprograms$ sudo cp calculator.sh /usr/local/bin/
        21:54:00 fredrik@hiekkis:~/koodit/myprograms$ cd /usr/local/bin/
        21:54:14 fredrik@hiekkis:/usr/local/bin$ ls
        calculator.sh  chucknorris  greetme

Muutan nimen niin että muistan nimen.

Tarkistan oikeudet tiedostolle ja teen tarvittavat muutokset siten että omistajalla on RWX oikeudet ja muilla on R-X oikeudet:

        21:55:56 fredrik@hiekkis:/usr/local/bin$ sudo chmod ugo+rx 
        calculator   chucknorris  greetme      
        21:55:56 fredrik@hiekkis:/usr/local/bin$ sudo chmod ugo+rx calculator 
        21:56:22 fredrik@hiekkis:/usr/local/bin$ ls-l
        bash: ls-l: command not found
        21:56:24 fredrik@hiekkis:/usr/local/bin$ ls -l
        total 12
        -rwxr-xr-x 1 root root 118 11. 3. 21:54 calculator
        -rwxr-xr-x 1 root root 232 10. 3. 16:17 chucknorris
        -rwxr-xr-x 1 root root 612 10. 3. 16:24 greetme
        21:56:27 fredrik@hiekkis:/usr/local/bin$ 
        
Kokeilen toimiiko scripti käyttäjällä:

        21:56:27 fredrik@hiekkis:/usr/local/bin$ calculator 
        Ensimäinen numero
        1
        Toinen numero
        2
        Laskutoimituksen tulos on 3
        
Toimiihan se. Muutan käyttäjää ja kokeilen sitten. 

        21:56:56 fredrik@hiekkis:/usr/local/bin$ sudo su putte   
        putte@hiekkis:/usr/local/bin$ calculator 
        Ensimäinen numero
        1
        Toinen numero
        2
        Laskutoimituksen tulos on 3
        putte@hiekkis:/usr/local/bin$ 
        
Toimiihan se. 

### Python komento

Teen ohjelman joka etsii tiedoston hakemistosta tai sen alihakemistoista.

        #!/usr/bin/python3
        import os
        import sys
        #Tuo tietoja järjestelmästä
        if len(sys.argv) != 2:
            print("Usage: {} <filename>".format(sys.argv[0]))
            sys.exit(1)
        #Tarkasta että komennon jälkeen tulee joku argumentti joka on vähintään 2 merkkiä pitkä
        filename = sys.argv[1]
        #määritä muuttujaksi "filename" käyttäjän syöte
        for root, dirs, files in os.walk('.'):
            if filename in files:
                filepath = os.path.join(root, filename)
                #Koodi käy läpi kaikki tiedostot siinä hakemistossa missä käyttäjä on.
                print(os.path.realpath(filepath))
                #Printtaa "realpath" tiedon löydetystä tiedostosta.
                sys.exit(0)
                #Lopettaa for loopin
        print("Error: File not found: {}".format(filename))
        # Kun scripti on käynnyt kaikki tiedostot läpi mutta ei löydä tiedostoa printtaa käyttäjän antaman syötteen.
Lähde: https://www.tutorialspoint.com/file-searching-using-python


Loin tiedoston käyttäjän omiin kansiohin `/home/fredrik/koodit/myprograms`

Kopioin tämän tiedoston hakemistoon `sudo cp whereis /usr/local/bin`

Vaihdan käyttöoikeudet tiedostolle `sudo chmod ugo+rx whereis`

Kokeilen toimiiko ohjelma: 

        22:29:55 fredrik@hiekkis:/$ whereis greetme
        /usr/local/bin/greetme
        22:30:13 fredrik@hiekkis:/$ whereis whereis.py
        /home/fredrik/koodit/myprograms/whereis.py
        22:30:28 fredrik@hiekkis:/$ whereis wäärätiedosto
        Error: File not found: wäärätiedosto
        22:30:37 fredrik@hiekkis:/$ 
        
Huomasin kun menin juurikansioon ja ajoin ohjelman että haussa kesti hieman aikaa. Tietysti koska tiedostoja oli enemmän haettavana.


### Bash scripti joka tekee jotain monelle tiedostolle.

Teen uuden bash script tiedoston ja lisään tiedostoon `echo moi` ja katson toimiiko se:

                20:26:23 fredrik@hiekkis:~/koodit/myprograms$ bash testi.sh 
                moi
                
En löytänyt mitään järkeviä ohjeita taikka neuvoja miten tekisin samanlaisen scriptin mitä Tero näytti tunnilla.

Jätin viimeisen tehtävän tekemättä.












