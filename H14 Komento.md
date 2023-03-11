## H14 Komento

Viikon tehtävänä on luoda uusia komentoja linux terminaaliin. Tehtävänä on luoda bashilla ja pythonilla komento jota kaikki käyttäjät voivat käyttää.
Viime viikon tehtävässä innostuin ja tein tämän tehtävän jo. Tämän viikon tehtäväksi teen saman mutta yritän hieman monimutkaistaa komentoa.

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
        



