# H4

### How to Handle Secrets on the Command Line

#### CARL TASHIAN 2021-03-12 , https://smallstep.com/blog/command-line-secrets/

- Blogipostaus devaajalta jossa hän kertoo mitä haavoittuvuuksia salaisilla tiedostoilla on eri ympäristöissä
- Hän selittää myös miten komentokehotetta voi käyttää hyvääkseen jotta saa selville käyttäjien "salaisia" tietoja
- Postauksessa Carl varoittaa miten välttää salassa pidettävien tiedostojen päätyvän komentoihin jotka on nähtävillä kaikilla


        "Avoid leaking the secret in the command string eg. with "$(< secret_file.txt)""
  
- Carl kertoo postauksessa seuraavan "Using environment variables for secrets is very convenient. And we don’t recommend it because it’s so easy to leak things"
- Se tarkoittaa, että käyttämällä ympäristömuuttujia salauksen tallentamiseen on helppo hallita ja käyttää näitä tietoja sovelluksessa. Kuitenkin, koska ympäristömuuttujat ovat helposti saatavilla ja niitä voidaan jakaa helposti, niiden käyttäminen salausten tallentamiseen on riskialtista ja suuria salaisuuksia ei tulisi tallentaa näin.



###  Syslog

