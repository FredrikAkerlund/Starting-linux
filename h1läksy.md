# Raportti Debian asennuksesta VMware:lle


## Perustiedot
Alusta: Lenovo ideapad 5 pro, R7 5000 sarja 16gt ram, 500gt SSD

OS: Windows 11

Aika: UTC+2 1800 17/01/23

Sijainti: Helsinki, 00140

Yhteys: DNA valokuituyhteys Download 100mpbs, upload 50mbps


## Vaiheet

1: Etsin uusimman Debian Distron https://cdimage.debian.org/images/unofficial/non-free/images-including-firmware/current-live/amd64/iso-hybrid/ osoitteesta. Linkki löytyy https://terokarvinen.com/2021/install-debian-on-virtualbox/ 

18:00

2: Uusin Distro on julkaistu 2022-12-17 14:17. Tiedoston nimi on debian-live-11.6.0-amd64-xfce+nonfree.iso.
Latasin tiedoston. Lataukseen meni 4min.

1806

3: Etsin uusimman version Oracle Virtualbox. Uusin julkaisu löyty https://www.virtualbox.org/. Lataus kesti n3min.

1810

4: Päivitin Virtualboxin uusimpaan 7.0 versioon

1815

5: Seurasin ohjeita Debian distron asennukseen. Ohjeet löytyivät sivustolta https://terokarvinen.com/2021/install-debian-on-virtualbox/

5.1: 	Seurasin ohjeita sanasta sanaan. Ohjeet täsmäsivät täysin oman ohjelmistoversioideni kanssa <br>

1830

6: Käynnistäessa virtuaalikonetta, Näyttö oli pitkään pimeä n.3min. Odottelun jälkeen työpöytä tuli hiljalleen näkyviin


Linux OS resoluutio on pielessä ja ruudussa näkyy vain pieni osa työpöytää

		Korjaus: Näytön kulmassa näkyi painike josta pystyin vaihtamaan reson scaled jolloin työpöytä mahtui näytölle

Virtualbox käyttöliittymän resoluutio muuttui samalla ja käyttäminen muuttui hieman vaikeammaksi.

Alkuperäinen painike mikä näkyi virtualboxin palkissa katosi. En pysty enään korjaamaan resoluutiota virtualboxin kautta.

1838

7: 	Ajaessani Install /debian ohjelman ohjeklma näytti varoituksen "The systmen is not pluggen in to a power source"

1840

8: Loin käyttäjätunnuksen, vahvan salasanan ja nimesin tietokoneen "hiekkis" nimeksi. Asennus alkoi 1840

9: Asennusvalmis 1851

10: KÄyttöjärjestelmä ei hyväksy luotu salasanaa. Salasana oli talletettu .txt tiedostoon. 

	Korjaus: Kokeile uudestaan päivällisen jälkeen

1922

11: Sammutin virtuaalikoneen sekä virtual box sovelluksen. Käyttöjärjestelmä hyväksyi käyttäjän ja salasanan.

1930

12: Debian distro asenettu virtuaalikoneelle

	Huomio: Resoluutio ei ole silmämielyttävä. Korjaa se Virtualboxin kautta
  
