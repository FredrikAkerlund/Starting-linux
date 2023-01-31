# HelloWeb

### Aloitus

Aloitin poistamalla apache weppipalvelimen

        sudo apt-get purge apache2 apache2-bin
        
Lisäksi pysäytin varmuuden vuoksi apache2 palvelun/demonin. En tiedä oliko tämä vaihe pakko mutta välttääksen tulevia ongelmia käytin komentoa:

        sudo systemctl stop apache2
        
### Apache2 asennus

Asensin apache2 webpalvelimen uudestaan. Käytin seuraavia komentoja:
  
        sudo apt-get install apache2
              
        sudo systemctl start apache2
        
Testasin weppipalvelimen toiminnan kirjoitin `localhost` selaimeen.
-  Selain vei minut apachen testisivulle
        
Saadakseni käyttöön käyttäjän hakemiston käytin seuraavaa komentoa:

        sudo a2enmod userdir

Jotta apache webipalvelin ottaa muutokset käyttöön palvelu on käynistettävä uudelleen

        sudo systemctl restart apache2
        
Saadakseni testisivun pois käytöstä tein seuraavat toimenpiteet:              

- Muokkasin hakemistosta `var/www/html` tiedostoa index html
- Käyttäen Micro ohjelmaa korvasin sisällön `Hello` tekstillä
- Komenolla `micro var/www/html/index.html` pääsin muokkaamaan tiedostoa helposti hyödyntäen micron helppokäyttöisyyttä

![var apache](https://user-images.githubusercontent.com/122887178/215842158-847200dc-0011-4f5e-b75a-4e342b560b8c.jpg)

### Käyttäjien kotisivujen muokkaus

Saadakseni käyttäjän kotisivun käyttöön minun piti luoda kansio nimeltä `public_html` käyttäjän kotihakemistoon `/home/fredriktest`



Koska `public_html` on tyhjä hakemisto selain ohjasi minut `index of /~fredriktest sivulle`

![Fredriktest](https://user-images.githubusercontent.com/122887178/215848818-c2824287-9f2d-4ed9-8b52-f37b6ee24e36.jpg)

Saadakseni haluamani kotisivu näkyviin loin uuden tiedoston `public_html` Hakemistoon

      micro index.html
      
      - Tiedostoon kirjoitin `Moro teretulemast mun sivuille`


![Teretulemast](https://user-images.githubusercontent.com/122887178/215849350-54b4d932-5cd1-4952-be96-c4a52599138c.jpg)

HUOM! Index hakemisto tuli näkyviin `localhost/~fredriktest` koska loin tämän käyttäjän aikasemmin enkä ollut lisännyt sinne mitään index.html

### Validoidun HTML5 sivun luominen weppipalvelimelle

1. Loin tiedoston `/home/fredrik/public_html` hakemistoon nimeltä `testi`

                micro testi
                
2. Kopioin vanhasta koulutyöstäni valmiin HTML5 validoidun kotisivun. 
3. Selaimen avulla siirryin HTML5 sivulle `http://localhost/~fredrik/testi`

![Kotisivu](https://user-images.githubusercontent.com/122887178/215851216-42748896-86f2-4856-8ee7-d485d3fa5f67.jpg)

HUOM! Alkuperäinen sivu jonka koodasi meni rikki koska irroitin yksittäisen HTML tiedoston hakemistosta. Tiedostossa oli linkkejä ja viittauksia jotka vaativat muita tiedostoja toimiakseen. `fredrik` hakemistoon olin jo luonnut `index.html` tiedoston.


### Uuden käyttäjän luonti

Uuden käyttäjän luomiseksi käytin komentoa:

        sudo adduser fredriktest
        
Vaihtaakseni käyttäjää terminaalissa käytin seuraavia komentoja

      sudo su fredriktesti
      su=switchuser
        
Tein samat toimenpiteet käyttäjälle `fredriktesti` kuin tein pääkäyttäjälle kotihakemiston aktivoimiseksi:

    Saadakseni käyttäjän kotisivun käyttöön minun piti luoda kansio nimeltä `public_html` käyttäjän kotihakemistoon `/home/fredriktest`

    Koska `public_html` on tyhjä hakemisto selain ohjaaa minut `index of /~fredriktest sivulle`

  ![Fredriktest](https://user-images.githubusercontent.com/122887178/215848818-c2824287-9f2d-4ed9-8b52-f37b6ee24e36.jpg)

    Saadakseni haluamani kotisivu näkyviin loin uuden tiedoston `public_html` hakemistoon
    
    Nimesin tiedoston nimeksi `index.html`
    
    Tiedostoon kirjoitin `Teretulemast taas`
    
  ![Teretulemast taas](https://user-images.githubusercontent.com/122887178/215852885-e97d4d7a-8a1f-450a-aca8-86cdbc6face1.jpg)
  
  ### Yhteenveto
  
  Asennuksissa ei tullut mitään ongelmia vastaan. Tiistain luento kattoi laajasti tämän tehtävän.
  Hyödynsin tehtävässä tehtäviä edellisistä kursseista.


    
    


                              



