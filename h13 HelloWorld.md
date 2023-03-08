## Hello world in different languages

Tehtävänä oli tehdä `hello world` ohjelma kolmella eri kielellä.

Minä aion tehdä ohjelmat Javalla, Pythonilla ja C.

Aloitan helpommasta ja tehokkaamasta eli Python:

### Python

Debian distrossa on valmiiksi asenettu Python3.

Tarkastan versionumeron

          8:15:17 fredrik@hiekkis:~/publicwsgi$ python3 --version
          Python 3.9.2
          
Internetin mukaan tämä on uusin versio joka on jakelussa debian käyttöjärjestelmälle.

Luon uuden tiedoston `hello.py` ja tiedostoon kirjoitan seuraavan:

          print("Hello world in python")
          
          8:18:43 fredrik@hiekkis:~/koodit$ python3 hello.py 
          Hello world in python
          
Tämä oli äärimmäisen helppoa

### C

Tarkastan mikä versio C:stä on asenettu valmiiksi debian distroon

          18:20:53 fredrik@hiekkis:~/koodit$ gcc --version
          gcc (Debian 10.2.1-6) 10.2.1 20210110
Näyttäisi olevan oikea

Luon uuden tiedoston nimeltä `hello.c` ja kopion TeroKarvisen kirjoituksesta oikean koodin

          #include <stdio.h>
          int main()
          {
           printf("Hello world in C\n");
          }
Jotta voin käyttää juuri luomaani ohjelmaa se pitää koota (Compile). Tämä tapahtuu seuraavalla komenolla:

          18:25:10 fredrik@hiekkis:~/koodit$ gcc -o hello.c helloc
          
Tämä luo ajettavan ohjelman nimeltä `helloc`. Jotta voin ajaa ohjelmaa annan seuraavat komennot:

          18:25:32 fredrik@hiekkis:~/koodit$ ./helloc
          Hello world in C
          
C vaikuttaa erittäin haastavalta kieleltä. En haluaisi käyttää tätä mihinkään.

### JAVA

Käyttääkseni Javaa minun pitää asentaa `Java-jre` sekä `java-jdk`

`java-jdk` = Java development kit // Tällä mahdollistetaan Javan kirjoittaminen


`java-jre` = Java runtime enviroment // Tällä koostetaan java koodi ajettavaksi ohjelmaksi

Teen uuden tiedoston nimeltä `hello.java` ja kopioin TeroKarvisen kirjoituksesta koodinpätkän

                    public class hello
                    {
                     public static void main(String[] args)
                     {
                     System.out.println("Hello world in JAVA");
                     }
                    }
                    
Tämä tiedosto pitä koota samalla tavalla kuin C:ssä

                    8:34:25 fredrik@hiekkis:~/koodit$ javac hello.java
Tämä luo ajettavan ohjelman nimeltä `hello.class`

Tässä vaiheessa huomasin että tiedosto jossa koodi on pitää olla nimetty oikein. Nimi pitää olla sama kuin koodissa lukee `public class hello`. Tein virheen tässä ja `java-jre` ei suostunut luomaan ajettavaa ohjelmaa. Aiheutan ongelman uudestaan nimeämällä tiedoston uudestaan

          18:35:12 fredrik@hiekkis:~/koodit$ mv hello.java helloworld.java
          18:37:12 fredrik@hiekkis:~/koodit$ javac helloworld.java 
          helloworld.java:1: error: class hello is public, should be declared in a file named hello.java
          public class hello
                 ^
          1 error
          
En enempää tutkinut asiaa. Virheilmoitus kertoi hyvin miten asia korjataan.

Ajaekseni ohjelman annan seuraavan komennon:

          8:38:59 fredrik@hiekkis:~/koodit$ java hello
          Hello world in JAVA
          
#### Yhteenveto

Java ja C näyttää myös hirmu vaikealta ja monimutkaiselta. Onneksi aion keskittyä Python kieleen.

Lopuksi tein viellä scriptin joka ajaa kaikki 3 ohjelmaa suoraan terminaaliin.
          
          
          python3 hello.py
          ./helloc
          java hello

          8:40:56 fredrik@hiekkis:~/koodit$ bash helloworld.sh 
          Hello world in python
          Hello world in C
          Hello world in JAVA

Noin ikkääs on ohjelmat luotu ja tehty scripti.
Tämän viikon tehtävä oli mielestäni erittäin helppo. Jatkan vaihtoehtoisia osioita myöhemällä ajalla.


## Vapaaehtoinen osuus. Luo Greetme ohjelma

Aloitan luomalla uuden kansion mihin lisään itse tehdyt ohjelmat. Luon kansion nimeltä `myprograms`

          09:22:56 fredrik@hiekkis:~/koodit$ ls
          helloc  hello.class  hello.java  hello.py  helloworld.sh  myprograms
Kansioon luon python ohjelman joka vastaa `greetme` komentoon `Hello {user}`

          #!/usr/bin/env python3

          import os

          user = os.getlogin()
          print(f"Hello {user}!")
          
Koodi selitettynä:

- import os = mahdollista ohjelman käsitellä käyttöjärjestelmää
- user = os.getlogin () = Ohjelma hakee tiedon kuka on kirjautuneena järjestelmään
- print(f"Hello {user}!") = Ohjelma tulostaa "Hello {kirjautunut käyttäjä[

Kokeilen että ohjelma toimii kansiossa.

          09:27:36 fredrik@hiekkis:~/koodit/myprograms$ python3 greetme.py 
          Hello fredrik!
Jotta ohjelma on käytettävissä kaikilla käyttäjillä kaikissa terminaaleissa muutan ohjelman oikeuksia ja lisään tiedoston`/usr/local/bin/greetme ` hakemistopolun oikeaan ohjelmaan.

          09:27:42 fredrik@hiekkis:~/koodit/myprograms$ sudo micro /usr/local/bin/greetme
          
          #!/bin/bash

          /home/fredrik/koodit/myprograms/greetme.py
          
          
Kokeilen tässä vaiheessa mitä järjestelmä sanoo jos yritän ajaa ohjelmaa.

          09:31:54 fredrik@hiekkis:~/koodit/myprograms$ greetme
          bash: /usr/local/bin/greetme: Permission denied
          
Tästä voin vetää johtopäätökset että ohjelma on oikeassa paikassa mutta tiedoston oikeudet eivät ole kunnossa. Korjaan tämän

          09:42:01 fredrik@hiekkis:~/koodit/myprograms$ chmod 755 -R /home/fredrik/koodit/myprograms/
          09:42:11 fredrik@hiekkis:~/koodit/myprograms$ ls -l
          total 4
          -rwxr-xr-x 1 fredrik fredrik 80  8. 3. 09:17 greetme.py
Kokeilen toimiiko ohjelma

          09:42:12 fredrik@hiekkis:~/koodit/myprograms$ greetme
          bash: /usr/local/bin/greetme: Permission denied
          
Eli oikeudet on vieläkin väärät. Tarkistan mitkä oikeudet ohjelmalla greetme hakemistossa `/user/local/bin/greetme ` on.

          09:42:50 fredrik@hiekkis:~/koodit/myprograms$ ls -l /usr/local/bin |grep greetme
          -rw-r--r-- 1 root root 57  8. 3. 09:31 greetme
Tämä kertoo minulle että vain `Root` on oikeudet. Muutan oikeudet

          09:47:37 fredrik@hiekkis:~/koodit/myprograms$ sudo chmod 755 /usr/local/bin/greetme 
          09:47:43 fredrik@hiekkis:~/koodit/myprograms$ ls -l /usr/local/bin 
          total 4
          -rwxr-xr-x 1 root root 57  8. 3. 09:31 greetme
Kokeilen toimiiko `greetme`
                    
          09:47:48 fredrik@hiekkis:~/koodit/myprograms$ greetme
          Hello fredrik!
          
Sehän toimii.

Teen ohjelmasta hieman mielenkiintoisemman:

          #!/usr/bin/env python3

          import os
          import datetime

          user = os.getlogin()

          now = datetime.datetime.now()

          formatted_date_time = now.strftime("%d.%m.%Y %H:%M")
          print(f"Hello {user}!")
          print(f"Päivämäärä ja kello: {formatted_date_time}")
          print(f"Olet tällä hetkellä kansiossa: {os.getcwd()}")
          
Nyt ohjelma tulostaa seuraavat tiedot: 

        10:37:20 fredrik@hiekkis:~/koodit/myprograms$ greetme
          Hello fredrik!
          Päivämäärä ja kello: 08.03.2023 10:37
          Olet tällä hetkellä kansiossa: /home/fredrik/koodit/myprograms 
          Muista verrytellä ja punnerella työn ohessa. Muuten backend hommat muuttuu hirmu raskaiksi


## Yhteenveto

Minun koodauskokemukseni on erittäin rajallinen. Olen vain suorittanut peruskurssin Javascriptistä ja siitä ei ole hirveästi hyötyä backend työskentelyssä.
Kuitenkin Python kieli vaikuttaa logiselta ja odotan että pääsen suorittamaan lisää kursseja ja oikeasti oppimaan koodamaan Pythonilla.









                    

