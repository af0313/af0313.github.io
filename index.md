---
title: Portfolio - af0313
description: Ticorporate
---

# Portfolio

## Sisällysluettelo

1.  [Pilvi-infrastruktuuri](#1-pilvi-infrastruktuuri)
2.  [Testaus](#2-testaus)
3.  [Automaatio](#3-automaatio)
4.  [Muuta satunnaista](#4-muuta-satunnaista)
5.  [Tavoitteet](#5-tavoitteet)

## 1. Pilvi-infrastruktuuri

### Suunnitelmat

Projektin alkaessa pilvi-infrastruktuurista keskusteltiin ryhmän tekniikkajäsenten kesken; päädyimme kokeilemaan serverless-ratkaisua, koska halusimme haastaa itseämme. B-suunnitelmana suunniteltiin myös perinteisempi palvelinratkaisu (EC2-pohjainen). Ensimmäinen versio infrastruktuurisuunnitelmasta saatiin toteutettua aikaisin, mutta huomioimatta jäi se, miten sovellus saadaan käyttäjän laitteelle. Tämä korjattiin opettajan palautteen ansiosta. Alla on kuva ensimmäisestä käyttövalmiista suunnitelmasta.

![Infrastruktuuri-suunnitelma 1](assets/cloud1.png)

Lyhyesti selitettynä: käyttäjä pääsee sivulle CloudFrontin kautta S3:een tallennettuun sivuun, jonka jälkeen käyttäjä autentikoituu Cogniton avulla. Autentikoitu käyttäjä voi käyttää API Gatewayn kautta Lambda-funktioita, jotka keskustelevat RDS-tietokannan kanssa. Tarkoituksena oli myös implementoida omien kuvien tallentaminen sekä kuvatunnistusominaisuus Bedrockin avulla.

Projektin alkuvaiheessa kuitenkin ymmärrettiin, että toiminnallisuuksia tulee rajata rajallisen ajan vuoksi. Kuvantunnistusominaisuus rajattiin pois toteutuksesta. Myös koodikäytänteitä sovittaessa päädytiin käyttämään Github Actionseja CodePipelinen sijaan. Lisäksi backend-kehityksessä ilmeni toive lisätä RDS Proxy infrastruktuuriin tietokantalatenssin välttämiseksi. Alla olevassa kuvassa näkyvät nämä muutokset.

![Infrastruktuuri-suunnitelma 2](assets/cloud2.png)

### Hinta-arviot

Projektin alkuvaiheessa tehtiin arvio infrastruktuurin kuluista. Arvio perustui suunniteltuun infrastruktuuriin. Valitut resurssit eivät kuitenkaan kuvastaneet oikeassa käytössä valittuja, vaan olivat resurssi-intensiivisempiä ja sen takia kalliimpia. Oletuksena oli myös, että infrastruktuuria pidetään käynnissä vain työaikoina. Ensimmäinen arvio oli, että kulutus olisi noin 23 € kuussa. Todellisuudessa keskimääräinen kulutus oli noin 20 € kuussa. Arvio oli yllättävän realistinen huomioiden epävarmuuden suunnittelussa.

Kulutuksen vähentämisessä hyödynnettiin infrastruktuurin pystytystä ja tuhoamista IaC-templaattien avulla. Tulevaisuudessa projektin parissa voitaisiin miettiä EventBridgen hyödyntämistä esim. tietokannan pysäyttämiseen ajoitetusti.

### Ongelmatilanteet

Pilvi-infrastruktuurin parissa ilmeni hyvin vähän ongelmia. Omista ongelmista mainittakoon tässä kaksi tapausta:

- Aliverkkorakenteen takia hyppykoneen käyttäminen osoittautui hankalaksi RDS-tietokannan kanssa. Tapausta hankaloitti se, että käytimme RDS Proxyä. Lopulta ongelma kierrettiin luomalla migrations-lambda, jolla tietokantaan syötetään pystytyksen yhteydessä dataa.
- RDS Proxy ei kuulu AWS:n Free Tierin palveluihin. Kyseinen palvelu saatiin kuitenkin pystytettyä CDK-templaatilla. Amazon tilkitsi tämän porsaanreiän projektin aikana, mikä johti orporesurssin syntymiseen, sekä äkkinäiseen pilvi-infrastruktuurin muutokseen. Infrastruktuuri muutettiin sellaiseksi, että lambdat keskustelevat suoraan RDS-tietokannan kanssa.

### Mitä opin?

Opin sen, että dokumentaatiota seuraamalla saa rakennettua myös sellaisia infrastruktuureja, jotka eivät ole tyypillisiä. Kantapään kautta opin myös sen, että käyttöön otettavat palvelut kannattaa tarkistaa saatavuudeltaan (RDS Proxy!). Lisäksi opin paljon admin-työskentelystä AWS:n parissa.

Koin myös, että hyödynsin hyvin automaatiota pilven kanssa. Oli myös ilo huomata SQL- ja tietokantaosaamisen hyödyt, kun kyselyitä suunniteltiin. Parannettavaakin kuitenkin on: kulujen suunnitteluun ja seurantaan tulisi kiinnittää enemmän huomiota, kuten myös tileihin liittyvään tietoturvaan.

## 2. Testaus

Mitä opin? Vahvuudet tässä?

Miksi valitsin nämä esimerkit?

### Jasmine & Karma

Esimerkki.

### Jest

Esimerkki.

## 3. Automaatio

Mitä opin? Vahvuudet tässä?

Miksi valitsin nämä esimerkit?

### Github Actions Workflows

Github Actions workflows (deploy to s3 from hosting).

### Datan hankinta

Robotista pieni pätkä.

## 4. Muuta satunnaista

Mitä opin? Vahvuudet tässä?

Miksi valitsin nämä esimerkit?

### Mock data

Mock data (ja storen käyttö).

### Skriptit

Powershell.

### Frontend

Userpages.

## 5. Tavoitteet

### Reflektio

Miten tavoitteet toteutuivat? Mitä jäi toteutumatta? Mitä opin suunnitelman ulkopuolelta?

### Tulevaisuus

Tulevaisuus; millainen työ, kuinka TC tuki tätä, mitä osaamista vielä tarvitsen?
