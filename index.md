---
title: Portfolio - af0313
description: Ticorporate
---

![Tekijän profiilikuva](assets/profiili.jpg){: style="float: right; border-radius: 50%; margin: 1rem"}

# Portfolio

## Sisällysluettelo

1.  [Pilvi-infrastruktuuri](#1-pilvi-infrastruktuuri)
2.  [Testaus](#2-testaus)
3.  [Automaatio](#3-automaatio)
4.  [Muuta satunnaista](#4-muuta-satunnaista)
5.  [Tavoitteet](#5-tavoitteet)

Linkki projektin Github-repositorioon: [Atra](https://github.com/jamktiko/Atra)
Sähköposti: af0313@student.jamk.fi

## 1. Pilvi-infrastruktuuri

### Suunnitelmat

Projektin alkaessa pilvi-infrastruktuurista keskusteltiin ryhmän tekniikkajäsenten kesken; päädyimme kokeilemaan serverless-ratkaisua, koska halusimme haastaa itseämme. B-suunnitelmana suunniteltiin myös perinteisempi palvelinratkaisu (EC2-pohjainen). Ensimmäinen versio infrastruktuurisuunnitelmasta saatiin toteutettua aikaisin, mutta huomioimatta jäi se, miten sovellus saadaan käyttäjän laitteelle. Tämä korjattiin opettajan palautteen ansiosta. Alla on kuva ensimmäisestä käyttövalmiista suunnitelmasta.

![Infrastruktuuri-suunnitelma 1](assets/cloud1.png){: style="width:80%"}

Lyhyesti selitettynä: käyttäjä pääsee sivulle CloudFrontin kautta S3:een tallennettuun sivuun, jonka jälkeen käyttäjä autentikoituu Cogniton avulla. Autentikoitu käyttäjä voi käyttää API Gatewayn kautta Lambda-funktioita, jotka keskustelevat RDS-tietokannan kanssa. Tarkoituksena oli myös implementoida omien kuvien tallentaminen sekä kuvatunnistusominaisuus Bedrockin avulla.

Projektin alkuvaiheessa kuitenkin ymmärrettiin, että toiminnallisuuksia tulee rajata rajallisen ajan vuoksi. Kuvantunnistusominaisuus rajattiin pois toteutuksesta. Myös koodikäytänteitä sovittaessa päädyttiin käyttämään Github Actionseja CodePipelinen sijaan. Lisäksi backend-kehityksessä ilmeni toive lisätä RDS Proxy infrastruktuuriin tietokantalatenssin välttämiseksi. Myös aliverkkojen hierarkia selkeytyi suunnittelutyössä tarkemmaksi. Alla olevassa kuvassa näkyvät nämä muutokset.

![Infrastruktuuri-suunnitelma 2](assets/cloud2.png){: style="width:80%"}

### Hinta-arviot

Projektin alkuvaiheessa tehtiin arvio infrastruktuurin kuluista. Arvio perustui suunniteltuun infrastruktuuriin. Valitut resurssit eivät kuitenkaan kuvastaneet oikeassa käytössä valittuja, vaan olivat resurssi-intensiivisempiä ja sen takia kalliimpia. Oletuksena oli myös, että infrastruktuuria pidetään käynnissä vain työaikoina. Ensimmäinen arvio oli, että kulutus olisi noin 23 € kuussa. Todellisuudessa keskimääräinen kulutus oli noin 20 € kuussa. Arvio oli yllättävän realistinen huomioiden epävarmuuden suunnittelussa.

Kulutuksen vähentämisessä hyödynnettiin infrastruktuurin pystytystä ja tuhoamista IaC-templaattien avulla. Tulevaisuudessa projektin parissa voitaisiin miettiä EventBridgen hyödyntämistä esim. tietokannan pysäyttämiseen ajoitetusti.

### Ongelmatilanteet

Pilvi-infrastruktuurin parissa ilmeni hyvin vähän ongelmia. Omista ongelmista kuitenkin mainittakoon tässä kaksi merkittävää tapausta:

- Aliverkkorakenteen takia hyppykoneen käyttäminen osoittautui hankalaksi RDS-tietokannan kanssa. Tapausta hankaloitti se, että käytimme RDS Proxyä. Lopulta ongelma kierrettiin luomalla migrations-lambda, jolla tietokantaan syötetään pystytyksen yhteydessä dataa.
- RDS Proxy ei kuulu AWS:n Free Tierin palveluihin. Kyseinen palvelu saatiin kuitenkin alunperin pystytettyä CDK-templaatilla. Amazon tilkitsi tämän porsaanreiän projektin aikana, mikä johti orporesurssin syntymiseen sekä äkkinäiseen pilvi-infrastruktuurin muutokseen. Infrastruktuuri muutettiin sellaiseksi, että lambdat keskustelevat suoraan RDS-tietokannan kanssa.

### Mitä opin?

Opin sen, että dokumentaatiota seuraamalla saa rakennettua myös sellaisia infrastruktuureja, jotka eivät ole tyypillisiä. Kantapään kautta opin myös sen, että käyttöön otettavat palvelut kannattaa tarkistaa saatavuudeltaan (RDS Proxy!). Lisäksi opin paljon admin-työskentelystä AWS:n parissa. Ongelmatilanteisiin törmätessä suunnittelin muiden tekniikkavastaavien kanssa sen, miten ongelma korjataan tai kierretään.

Koin myös, että hyödynsin hyvin automaatiota pilven kanssa. Oli myös ilo huomata SQL- ja tietokantaosaamisen hyödyt, kun kyselyitä suunniteltiin. Parannettavaakin kuitenkin on: kulujen suunnitteluun ja seurantaan tulisi kiinnittää enemmän huomiota, kuten myös käyttäjiin liittyvään tietoturvaan.

## 2. Testaus

### Jasmine & Karma

Angular/Ionic-komponenttien testaus suoritettiin Jasminella ja Karmalla. Ratkaisuun päädyttiin, koska projektirakenteessa oli jo automaattisesti generoidut testit komponenteittain. Tavoitteena oli testata komponenttien päätoiminnallisuuksia. Alla on esimerkki Jasmine-testistä customers-page-komponentille. Testillä testataan filteredCustomers-funktion toimivuus.

```
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { HttpClientTestingModule } from '@angular/common/http/testing';
import { provideRouter } from '@angular/router';
import { CustomersPage } from './customers.page';

describe('CustomersPage', () => {
  let component: CustomersPage;
  let fixture: ComponentFixture<CustomersPage>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [HttpClientTestingModule, CustomersPage],
      providers: [provideRouter([])],
    }).compileComponents();

    fixture = TestBed.createComponent(CustomersPage);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should filter customers by search term', () => {
    component.allcustomers = [
      //mock customers; follows Interface
      {
        customer_id: 1,
        first_name: 'Tapio',
        last_name: 'Testinen',
        email: 'tapio@example.com',
        phone: '0401234567',
        notes: 'Testiasiakas',
        User_user_id: 'test',
      },
      {
        customer_id: 2,
        first_name: 'Matti',
        last_name: 'Meikäläinen',
        email: 'matti@example.com',
        phone: '0507654321',
        notes: '',
        User_user_id: 'test',
      },
    ];

    component.searchItem = 'tapio';
    let result = component.filteredCustomers();
    expect(result.length).toBe(1);
    expect(result[0].first_name).toBe('Tapio');

    component.searchItem = 'meikäläinen';
    result = component.filteredCustomers();
    expect(result.length).toBe(1);
    expect(result[0].last_name).toBe('Meikäläinen');

    component.searchItem = 'matti@';
    result = component.filteredCustomers();
    expect(result.length).toBe(1);
    expect(result[0].email).toBe('matti@example.com');

    component.searchItem = '';
    result = component.filteredCustomers();
    expect(result.length).toBe(2);

    component.searchItem = 'xyz';
    result = component.filteredCustomers();
    expect(result.length).toBe(0);
  });
});
```

### Jest

CDK-templaattien toimivuus testattiin Jest-kirjaston avulla, implementoituna julkaisuputkeen muiden yksikkötestien tavoin. Alla on esimerkki database-stackin testaamisesta. Esimerkissä on myös huomioitavaa aiemman riippuvuuden simulointi.

```
import { App } from 'aws-cdk-lib';
import { Template } from 'aws-cdk-lib/assertions';
import { DatabaseStack } from '../lib/database-stack';
import * as ec2 from 'aws-cdk-lib/aws-ec2';
import { Stack } from 'aws-cdk-lib';

describe('DatabaseStack', () => {
  let app: App;
  let template: Template;

  // beforeEach hook to set up the app and stack before each test
  // need to set up a fake VPC as well since DatabaseStack requires it
  beforeEach(() => {
    app = new App();
    const vpcStack = new Stack(app, 'TestVpcStack');
    const vpc = new ec2.Vpc(vpcStack, 'TestVpc', {
      maxAzs: 1,
      subnetConfiguration: [
        {
          name: 'Public',
          subnetType: ec2.SubnetType.PUBLIC,
          cidrMask: 24,
        },
        {
          name: 'Private',
          subnetType: ec2.SubnetType.PRIVATE_WITH_EGRESS,
          cidrMask: 24,
        },
        {
          name: 'Isolated',
          subnetType: ec2.SubnetType.PRIVATE_ISOLATED,
          cidrMask: 24,
        },
      ],
    });
    const instanceClass = ec2.InstanceClass.BURSTABLE3;
    const instanceSize = ec2.InstanceSize.MICRO;
    const dbStack = new DatabaseStack(app, 'TestDatabaseStack', {
      vpc,
      rdsSecretName: 'test-secret',
      instanceClass,
      instanceSize,
    });
    template = Template.fromStack(dbStack);
  });

  // tests that a secret is created in Secrets Manager with the correct name
  it('should create secrets in Secrets Manager', () => {
    template.hasResourceProperties('AWS::SecretsManager::Secret', {
      Name: 'test-secret',
    });
  });

  // tests that an RDS instance is created with the correct properties
  it('should create an RDS instance', () => {
    template.hasResourceProperties('AWS::RDS::DBInstance', {
      DBInstanceClass: 'db.t3.micro',
      Engine: 'mysql',
    });
  });
});

```

### Mitä opin?

Vaikka en tehnyt testaussuunnitelmaa alussa, sain silti suoritettua testausta suhteellisen järjestelmällisesti. Onnistuin identifoimaan tärkeimmät paikat, joissa testaamista tarvitaan. Koodiin liittyvät muutokset kuitenkin usein rikkoivat testit, jotka oli kirjoitettu aikaisessa vaiheessa projektia, ja aikataulusyistä näitä testejä mm. kommentoitiin pois. Testit oli toisaalta implementoitu järkevästi, koska yksikkötestit suoritettiin julkaisuputkessa automaattisesti. Testit suorittamalla jokaisen pushin yhteydessä löydettiin useita ongelmakohtia koodissa.

Toiseksi ongelmakohdaksi nousi modaalikomponentit. Käytännössä kaikki modaalikomponentit jäivät yksikkötestien sijaan e2e-testien testattaviksi. Lopulta kuitenkin myös e2e-testit jäivät implementoimatta lopputuotteelle, koska Cognito-ominaisuuden lisääminen koodissa esti Cypressiltä pääsyn sisäänkirjautumaan. Mikäli aikaa olisi ollut enemmän, eikä tarvetta siirtyä muihin työtehtäviin, olisin voinut käyttää lisätunteja ongelman ratkomiseen.

Lisäksi projektin loppupuolella ymmärsimme ryhmänä, että storen hyödyntäminen sovelluksessa olisi ratkonut useita ongelmia. Logiikan kulku, suorituskyky, ja testien kattavuus olisi parempi, jos olisimme tehneet niin.

## 3. Automaatio

### Github Actions Workflows

Projektia aloittaessa tahdoimme, että julkaisusta tehdään mahdollisimman helppo ja automatisoitu. Päädyimme käyttämään Github Actions workfloweja. Tehtävänäni oli rakentaa tarvittavat workflowit. Ryhdyin rakentamaan workfloweja askel kerrallaan demon perusteella. Kaiken kaikkiaan tein neljä workflowia:

- Destroy AWS Infrastructure: tuhoaa pilviresurssit. Tämänkaltaisita ei tulisi käyttää oikeissa tuotantoympäristöissä, mutta prototyypin rakentamisessa koen oikeutetuksi.
- Test on Main Push: ajaa yksikkötestit, kun githubiin tehdään push.
- Production Deploy: Pystyttää CDK-templaattien avulla AWS-resurssit. Tämä workflow myös ajaa Migrations-lambdan, jolla populoidaan tietokanta.
- Hosting Deployment: Buildaa sovelluksen, ja kopioi tuotoksen hosting-kansiosta haluttuun S3:een.

Hosting Deployment oli lopulta vähimmällä käytöllä, mutta muista kolmesta saimme tuntuvia hyötyjä. Yksikkötestien automaattisilla ajoilla löydettiin virheitä koodissa. Lisäksi saimme tuntuvia säästöjä rahallisesti resurssien käytöstä vain työaikana. Alla on esimerkkinä Test on Main Push.

```
name: Test on Main Push
run-name: ${{ github.actor }} is pushing to main
on:
  push:
    branches:
      - main
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "20"
      - name: Use npmmirror registry #temp fix for package problems
        run: npm config set registry https://registry.npmmirror.com
      - name: Install dependencies
        run: npm ci
      - name: Install Ionic CLI
        run: npm install -g @ionic/cli
      - name: Build Ionic projects
        run: ionic build
      - name: Run unit tests
        run: |
          npm test -- --watch=false --browsers=ChromeHeadless
          cd backend
          npm install
          npm test
      - name: Final status
        run: echo "This job's status is ${{ job.status }}."
```

### Datan hankinta

Toinen asia, jossa halusimme hyvissä ajoin tehdä asian itsellemme helpoksi oli oikean datan tutkiminen ja mahdollinen käyttöönotto. Otin työn alle Nordic Tattoo Suppliesin sivuilta datan käsittelyn. Datasta löytyi juuri sen muotoisia ominaisuuksia kuin oletimme, mutta esim. nimien pituuksissa tuli yllättäviä huomioita. Jouduimme tämän takia mm. muokkaamaan tietokannassa musteiden nimien sallittua pituutta.

Rakensin myös Python-pohjaisen ohjelman, jolla hain dataa sivuilta. Saimme luvan NTS:ltä tämän toteuttamiseen (ainoana toiveena oli, ettemme käyttäisi sivuistoilla näkyviä kuvia tekijänoikeusseikkojen takia). Ohjelmalle annetaan taulukkona sivut, joita käsitellään. Tämän jälkeen sivulta ekstraktoidaan dataa tietyistä elementeistä, joka taas muotoillaan haluttuun muotoon. Haasteita tässä oli se, että data ei ollut yhtenäisessä muodossa eri valmistajien tuotteissa.

Kun data on lopulta kerätty ja käsitelty, se muotoillaan vielä SQL-lauseiksi, joilla data voidaan syöttää helposti tietokantaan. Datan käsittely vaatii useita askelia; heittomerkkien käsittely SQL:ää varten, välilyöntien trimmaus, turhien sanojen poistaminen, sekä tuotepakettien erittely tuotteista. Alla on muutama esimerkki ohjelman toiminnasta.

Title-muuttujan transformointi käyttökelpoiseksi apufunktioiden avulla. Manufacturer ja size otetaan talteen muuttujiin ennen tätä elif-lauseilla. Ylläpidettävyys on tällä hetkellä huonohko, mutta uusia mustetuottajia lisätään sivuston tuotteisiin harvoin.

```
# product name is title minus manufacturer and size, plus some rubbish
product_name = title.replace(manufacturer, "").replace(size, "").strip(" -")
product_name = clean_title(product_name) # remove unnecessary words
product_name = normalize_whitespace(product_name) # clean up whitespace
product_name = product_name[:max_name_length]  # truncate safely
```

SQL-inserteiksi muuttaminen ja tallentaminen tiedostoon.

```
# Save SQL to file; header + values
sql_header = "INSERT INTO PublicInk (product_name, manufacturer, color, recalled, image_url, size) VALUES\n"
with open("inserts.sql", "w", encoding="utf-8") as f:
    f.write(sql_header)
    f.write(",\n      ".join(sql_values) + ";\n")

print("SQL statements saved to inserts.sql")
print("Total amount of inks fetched:", ink_amount)
# End of file: use inserts.sql to import data into the database.
```

### Mitä opin?

Alusta asti automaation miettimisestä oli suuria hyötyjä. Workflowit olivat erityisen hyödyllisiä, varsinkin kun yksikkötestit saatiin implementoitua niihin. Pyrin myös siihen, että olisimme hyödyntäneet Hosting Deployment-workflowia enemmän. Tässä kuitenkin päädyttiin ongelmiin sen suhteen, että vaihtuva API-url oli hankalaa päivittää automaattisesti pystytyksen jälkeen koodiin. Luultavimmin tämän olisi voinut ratkoa hyödyntämällä api-stackin output-arvoa url:n hakemiseksi.

Kokemuksena "robotin" rakentaminen oli kuitenkin antoisa ja mukava. Datan hankinta sen avulla jäi ikävä kyllä irralliseksi. Toisaalta, on hyvä tietää etukäteen jatkokehitystä varten datan muodosta ja siitä, miten paljon dataa tietokantaan on päätymässä.

## 4. Muuta satunnaista

### Mock data

Projektin aikaisissa vaiheissa ilmeni tarve päästä kehittämään frontendiä vaikka pilvi-infrastruktuurissa tai tietokantarakenteessa tehdään samanaikaisesti muutoksia. Lähestyin ongelmaa tekoälyn avustamana, ja sain tehtyä yksinkertaiset rakenteet, joilla env-tiedostossa saadaan määriteltyä näytetäänkö oikeaa "tuotantodataa" vai luotua mock dataa. Ongelmia ilmeni kuitenkin esim. mock-datan päivittämisessä, koska sovellus ei käytä storea datan käsittelyyn. Mock datasta saatiin kuitenkin hyötyjä; frontend-kehitys pystyi jatkumaan jouhevasti.

### Skriptit

Pienenä työn optimointina kirjoitin skriptejä, jotka nopeuttivat omaa tekemistäni. Ainakin yksi muu ryhmän jäsen myös innostui kokeilemaan omia skriptejä. Tein skriptit Powershellin profiiliin; alla on esimerkkejä.

```
function atra {
    Set-Location 'C:\JAMK\tc\atra_project\Atra'
}

function dep {
    cdk deploy vpc --require-approval never
    cdk deploy database --require-approval never
    cdk deploy frontend --require-approval never
    cdk deploy cognito --require-approval never
    cdk deploy api --require-approval never
}

function apiq {
    $stackName = "api"
    $stackOutputJson = aws cloudformation describe-stacks --stack-name $stackName | ConvertFrom-Json
    $outputs = $stackOutputJson.Stacks[0].Outputs
    foreach ($output in $outputs) {
        Write-Host "Output Key: $($output.OutputKey)"
        Write-Host "Output Value: $($output.OutputValue)"
        Write-Host "Description: $($output.Description)"
        Write-Host "-----------------------------"
    }
}
```

Skriptit tekevät seuraavat toimenpiteet:

- Siirtyminen projektin työkansioon.
- CDK-templaattien ajaminen peräkkäin ilman vaadittuja käyttäjäsyötteitä. Kyseistä skriptiä hyödynnettiin myös deploy-workflowissa.
- api-stackin outputtien haku; tällä saadaan nopeasti API-url.

### Frontend

Projektin loppupuolella siirryin auttamaan frontendin kehityksessä kevyemmissä työtehtävissä. Tein tyylittelyjä yhteisesti sovittuja tyylejä seuraten, mutta tein myös sisällöntuotantoa esim. Privacy Policy-sivulle User-tabin alle.

### Mitä opin?

Skriptien rakentamisen parissa opin lisää komentorivin käyttämisestä ja työn optimoinnista. Tämä toi varmuutta tekemiseen myös esim. Gitin parissa. Skriptien auttamana säästin useita työminuutteja, jotka olisivat muuten valuneet hukkaan. Mock datan tekeminen oli myös hyödyllistä, mutta koin sen myös haastavaksi. Sovelluksen rakenne oli mielestäni monimutkainen ja komponenttien uudelleenkäyttö vähäistä, mikä teki rakenteesta monimutkaisemman kuin tarve vaati. Kuten aiemmin mainittu, storea käyttämällä tämä ongelma oltaisiin saatu ratkaistua.

Jälkeenpäin mietittynä olisin voinut opiskella Angularia ja Ionicia aikaisessa vaiheessa, että työntekoni frontendin parissa olisi ollut hyödyllisempää. Tyylien asettelu elementeille on melko mekaanista työtä. Toisaalta käytin aikaa, että saimme huolellisesti suunnitellut rakenteet tulevaisuutta varten komponenteille, jotka eivät olleet vielä tärkeitä.

## 5. Tavoitteet

### Reflektio

En ollut asettanut tarkkoja tavoitteita opintojaksolle, mutta halusin päästä tekemään Scrum Masterin tai Product Ownerin roolissa töitä, sekä oppia joitakin teknologioita lisää. En kuitenkaan päässyt SM:n tai PO:n rooliin. Projektinhallintaan liittyen sain kuitenkin kokemusta Scrum-prosessista, ja pyrin myös pitämään itseni ja tiimini ajan tasalla tulevista deadlineista. Testaajana pääsin oppimaan uusia testikirjastoja, ja lisäksi panostin oma-aloitteisesti automaation opetteluun Githubissa. Opin myös Angularin perusteita.

Lisäksi opin myös paljon Gitin käytöstä tekemällä ryhmälle käyttöohjeet tyypillisiin tilanteisiin. Ongelmatilanteissa ratkoimme asiat yhdessä. Pilvi-infrastruktuurin suunnittelu ei ollut alkuperäisissä tavoitteissani, mutta sen tekeminen oli hyvä haaste itselleni. Toivoisin voivani myös myöhemmin työntää itseäni vielä enemmän mukavuusaluen ulkopuolelle oppimaan uutta.

### Tulevaisuus

Tulevaisuudessa toivoisin pääseväni töihin data-analytiikan parissa. Tässä pilvi-infrastruktuurin käsittelyssä on ehdottomasti hyötyä, kuten myös muualta hankitusta Python-osaamisesta. Testaus ja QA ei varsinaisesti liity tähän suoraan, mutta sain siitä myös hyviä oppeja matkaan. Opin myös yleisiä projekti- ja ohjelmistotuotantokäytänteitä, sekä erityisesti Scrumiin liittyvää osaamista.

Taitoja, joita täytyisi vielä harjoitella on oman tilastotieteen ymmärtämisen kasvatus, sekä syvempi ymmärrys data-analytiikasta. Koen, että näitä pääsisin parhaiten oppimaan oikean työn parissa teoreettisen suuntauksen sijaan. Valituilla opintojaksoilla olen päässyt melko pintapuolisesti tekemään data-analyysiä, mutta lähtökohdat ovat tällä hetkellä tyydyttävät.

[⬆ Back to top ](#portfolio)
