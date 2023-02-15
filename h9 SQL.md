## Yrityssofta

Stackoverflow.com käyttää SQL tietokantaa heidän palveluissaan. 

Lähde: https://stackoverflow.blog/2008/09/21/what-was-stack-overflow-built-with/

Tietokanta palvelimen käytössä on monia etuja. Tietokanta palvelin mahdollistaa tietoon pääse käsiksi moni käyttäjä monelta eri päätelaitteelta.<br>
Tietokanta palvelin pystyy myös määrittämään käyttäjille eri oikeuksia miten he voivat manipuloida tietokantaa. Käyttäjiä voidaan estää tai myöntää oikeuksia tietokantaan. Esimerkiksi esimiehet voivat saada oikeuden muokata ja poistaa henkilöstörekisteriä. Kun taas myyntipuoli on vain rajoitettu myyntiin liittyviin tietoihin.

Tietokanta palvelin myös edistää yrityksen tietoturvaa. Sen voi toteuttaa erittämällä tietokantapalvelimet toisistaan vaikeuttaen murtajan elämää.

Perinteiset muistilevyt voivat olla haavottuvaisia ja ovat alttiita fyysisille haitoille kuten iskut. Muistilevyt eivät ole skaalautuvia eivätkä sovellu monen käyttäjän samanaikaiseen käyttöön. Muistilevy vaati myös fyysisen pääsyn muistilevylle.

Lähde: https://www.indeed.com/career-advice/career-development/database-server

### Uuden käyttäjän ja tietokannan luonti PostgreSQL

- Luon uuden käyttäjän virtuaalikoneelle

          sudo adduser thor
          
- Luon uuden  PostgreSQL käyttäjän ja sille tietokannan 

              sudo -u postgres createuser thor
              sudo -u postgres createdb thor
              
- Vaihdan käyttäjää

              sudo su thor
- Thor käyttäjällä anna komennon `psql`
              
             
