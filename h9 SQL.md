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
                         
![Thor](https://user-images.githubusercontent.com/122887178/219110143-7d33a76f-71f5-497a-8f94-98ec79f70079.jpg)

- Nyt olen luonnut uuden käyttäjän ja tietokannan sille käyttäjälle


### Taulun teko ja muokkaaminen

- Luon uuden taulun käyttäjälle thor

                           
                    thor=> create table h9 (name varchar(200), age number, city varchar(200));
                    ERROR:  type "number" does not exist
                    LINE 1: create table h9 (name varchar(200), age number, city varchar...
                                                                    ^
                    thor=> create table h9 (name varchar(200), age int not null, city varchar(200));
                    CREATE TABLE
                    thor=> \d
                           List of relations
                     Schema | Name | Type  | Owner 
                    --------+------+-------+-------
                     public | h9   | table | thor
                    (1 row)
                    
- Kokeilen lisätä tauluun tietoja
- Minulla ei ole mitään kokemusta SQL kielestä ja tässä vaiheessa joudun tukeutumaan W3schools sivustoon.
- Lähde :https://www.w3schools.com/sql/sql_insert.asp

                      thor=> insert INTO h9 (name, age, city) values ('thor', 15, 'oslo'),
                    ('kalle', 29, 'helsinki'),
                    ('zeus', 30, 'kopenhagen');
                    INSERT 0 3
                   

- Törmäsin jatkuvasti syntax erroreihin mutta kokeilemalla sain lisättyä 4 riviä tietokantaan.
- Katson taulua mitä tietoja lisäsin tietokantaan.

                    thor=> select * from h9
                    thor-> ;
                     id | name  | age |    city    
                    ----+-------+-----+------------
                      1 | thor  |  15 | oslo
                      2 | thor  |  15 | oslo
                      3 | kalle |  29 | helsinki
                      4 | zeus  |  30 | kopenhagen
                    (4 rows)
                    
- Kokeilen lisätä sarakkeen tekemääni tauluuni

                              alter table h9 add country carchar(20);
                              
- Ja tämähän toimi monen yrityksen jälkeen

                    thor=> select * from h9
                    thor-> ;
                     id | name  | age |    city    
                    ----+-------+-----+------------
                      1 | thor  |  15 | oslo
                      2 | thor  |  15 | oslo
                      3 | kalle |  29 | helsinki
                      4 | zeus  |  30 | kopenhagen
                    (4 rows)
                    
- Seuraavaksi yritön poistaa kovan työn tuloksen
                    
                    thor=> drop table h9;
                    DROP TABLE

                    thor=> drop table h9;
                    DROP TABLE
                    
                    :(

- Toistan äskeisen ja lisään luotuun tauluun 4 saraketta

                    thor=> create table h9 (
                    thor(> name var

                    thor(> name varchar (20), 
                    thor(> age int not null, 
                    thor(> city varchar(20),
                    thor(> country varchar(20))
                    thor-> ;
                    CREATE TABLE
                    thor=> select * from h9 ;
                     name | age | city | country 
                    ------+-----+------+---------
                    (0 rows)
                    
- Nyt yritän lisätä rivin taulukkoon.

                    thor=> insert into h9 values ('kalle', 15, 'helsinki', 'finland');
                    INSERT 0 1
- Katson tuloksen

                    thor=> select * from 
                    h9                   information_schema.  pg_catalog.          pg_toast.            public.              
                    thor=> select * from 
                    h9                   information_schema.  pg_catalog.          pg_toast.            public.              
                    thor=> select * from h9;
                     name  | age |   city   | country 
                    -------+-----+----------+---------
                     kalle |  15 | helsinki | finland
                    (1 row)
                    
- Onnistuin tässä.


### Yhteenveto

- SQL kieli tuotti minulle suuria vaikeuksia mutta noin 1h kokeilun jälkeen sain perusajatuksesta kiinni. Kieli ei vaikuta niin monimutkaiselta.
- Syntaxi on melko yksinkertainen.
- Kurssi tietokantoihin auttaisi tässä huomattavasti.





   
