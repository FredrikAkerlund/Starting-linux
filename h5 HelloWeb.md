# HelloWeb

### Capturing a Valuable Market from the Bottom Up with Amjad Masad of Replit
#### Indiehackers podcast jossa vieraana Replit palvelun perustaja ja toimitusjohtaja Amjad Masad
Julkaistu 12 Jan 2022, Jakson kesto 37min, Kuunneltu Spotifyssa 01/02/23

Podcastissa Indie Hackersin Allen Courtland haastattelee miten Amjad Masad on luonnut palvelun Replit.<br>
Replit on selainpohjainen ympäristö jossa aloittelevat sekä kokeneemmat koodaajat pystyvät tuottamaan, jakaamaan ja kehittämään omia projekteja.


Amjad Masad varttui Jordaniassa jossa hän nuorena poikana tutustui tietokoneisiin isänsä kautta. 
Jo kuusi vuotiaana Amjad aloitti kehittäjän uransa.
Replit alkoi open source projektina jonka tarkoitus oli luodan nettipohjaisen ohjelmointiympäristön aloitteleville kehittäjille, jossa he voivat luoda ja kehittää omia palvelujaan.


Kehittäessä Replittiä Amjadin tarkoitus ei ollut kilpailla suurten toimijoiden kanssa vaan tarjota matalan tason koodajille oman alustan. Kun Replittiä kehitettiin suuret yritykset hallitsivat markkinoita. Näiden suurien yrityksen kohdeyleisö oli teollisuusyrittäjät ja muut korkean tason pelaajat.


Suuren suosion myötä Replitistä kasvoi suuri yritys ja tänä päivänä on yksi suosituimmista kehittämisympäristöistä.
Replit toimii Saas (Software as a Service) mallilla. Replit ei toimi Apache palvelimella vaan replit ympäristö toimii itse kehitetyssä palvelin arkitehtuurissa.

Podcastissa Amjad kertoo miten hän uransa aikana joutui luopumaan kehittämisestä, missä hän oli parhaimillaa, ja joutui keskittymään enemmän johtamiseen, hallintoon ja toimimaan toimitusjohtajana. Replitin tie ei ollut täysin ruusuinen. Replitin kasvun myötä alusta joutui hackereiden kohteeksi. Amjad kertoo miten hän joutui taistelemaan eri hyökkäyksiä vastaan vuorokauden ympäri.






## Apache webbipalvelun käyttöönotto 

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
      Tiedostoon kirjoitin `Teretulemast mun sivulle`
      Alla oleva kuva on toisen käyttäjän index sivulta


![Teretulemast](https://user-images.githubusercontent.com/122887178/215849350-54b4d932-5cd1-4952-be96-c4a52599138c.jpg)

HUOM! Index hakemisto tuli näkyviin `localhost/~fredriktest` koska loin tämän käyttäjän aikasemmin enkä ollut lisännyt sinne mitään index.html

### Validoidun HTML5 sivun luominen weppipalvelimelle

1. Loin tiedoston `/home/fredrik/public_html` hakemistoon nimeltä `testi`

                micro testi
                
2. Kopioin vanhasta koulutyöstäni valmiin HTML5 validoidun kotisivun. 
3. Selaimen avulla siirryin HTML5 sivulle `http://localhost/~fredrik/testi`

![Kotisivu](https://user-images.githubusercontent.com/122887178/215851216-42748896-86f2-4856-8ee7-d485d3fa5f67.jpg)

HUOM! Alkuperäinen sivu jonka koodasi meni rikki koska irroitin yksittäisen HTML tiedoston hakemistosta. Tiedostossa oli linkkejä ja viittauksia jotka vaativat muita tiedostoja toimiakseen. 

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


    
    


                              



