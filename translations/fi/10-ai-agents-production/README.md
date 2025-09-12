<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "cdfd0acc8592c1af14f8637833450375",
  "translation_date": "2025-08-29T17:09:59+00:00",
  "source_file": "10-ai-agents-production/README.md",
  "language_code": "fi"
}
-->
# AI-agentit tuotannossa: Havainnointi ja arviointi

[![AI-agentit tuotannossa](../../../translated_images/lesson-10-thumbnail.2b79a30773db093e0b4fb47aaa618069e0afb4745fad4836526cf51df87f9ac9.fi.png)](https://youtu.be/l4TP6IyJxmQ?si=reGOyeqjxFevyDq9)

Kun AI-agentit siirtyvät kokeellisista prototyypeistä todellisiin sovelluksiin, niiden käyttäytymisen ymmärtäminen, suorituskyvyn seuranta ja tuotosten systemaattinen arviointi tulevat yhä tärkeämmiksi.

## Oppimistavoitteet

Tämän oppitunnin jälkeen osaat/ymmärrät:
- Agenttien havainnoinnin ja arvioinnin keskeiset käsitteet
- Tekniikoita agenttien suorituskyvyn, kustannusten ja tehokkuuden parantamiseen
- Mitä ja miten arvioida AI-agenttejasi systemaattisesti
- Kuinka hallita kustannuksia AI-agenttien käyttöönotossa tuotantoon
- Kuinka instrumentoida AutoGenilla rakennetut agentit

Tavoitteena on antaa sinulle tietoa, jonka avulla voit muuttaa "musta laatikko" -agenttisi läpinäkyviksi, hallittaviksi ja luotettaviksi järjestelmiksi.

_**Huom:** On tärkeää ottaa käyttöön turvallisia ja luotettavia AI-agentteja. Tutustu myös [Luotettavien AI-agenttien rakentaminen](./06-building-trustworthy-agents/README.md) -oppituntiin._

## Jäljet ja spanit

Havainnointityökalut, kuten [Langfuse](https://langfuse.com/) tai [Azure AI Foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/what-is-azure-ai-foundry), esittävät agenttien suorituksia yleensä jälkinä ja spaneina.

- **Jälki** edustaa kokonaisvaltaista agentin tehtävää alusta loppuun (esim. käyttäjän kyselyn käsittelyä).
- **Spanit** ovat yksittäisiä vaiheita jäljen sisällä (esim. kielimallin kutsuminen tai datan hakeminen).

![Jälkipuu Langfusessa](https://langfuse.com/images/cookbook/example-autogen-evaluation/trace-tree.png)

Ilman havainnointia AI-agentti voi tuntua "mustalta laatikolta" – sen sisäinen tila ja päättely ovat läpinäkymättömiä, mikä vaikeuttaa ongelmien diagnosointia tai suorituskyvyn optimointia. Havainnoinnin avulla agentit muuttuvat "lasilaatikoiksi", mikä tarjoaa läpinäkyvyyttä, joka on elintärkeää luottamuksen rakentamiseksi ja sen varmistamiseksi, että ne toimivat tarkoitetulla tavalla.

## Miksi havainnointi on tärkeää tuotantoympäristöissä

AI-agenttien siirtyminen tuotantoympäristöihin tuo mukanaan uusia haasteita ja vaatimuksia. Havainnointi ei ole enää "kiva lisä", vaan kriittinen ominaisuus:

*   **Vianetsintä ja juurisyyn analysointi**: Kun agentti epäonnistuu tai tuottaa odottamattoman tuloksen, havainnointityökalut tarjoavat jälkiä, joiden avulla virheen lähde voidaan paikantaa. Tämä on erityisen tärkeää monimutkaisissa agenteissa, jotka voivat sisältää useita LLM-kutsuja, työkalujen vuorovaikutuksia ja ehdollista logiikkaa.
*   **Viiveen ja kustannusten hallinta**: AI-agentit luottavat usein LLM:iin ja muihin ulkoisiin API:hin, jotka laskutetaan tokenien tai kutsujen perusteella. Havainnointi mahdollistaa näiden kutsujen tarkan seurannan, mikä auttaa tunnistamaan toiminnot, jotka ovat liian hitaita tai kalliita. Tämä mahdollistaa tiimien optimoida kehotteita, valita tehokkaampia malleja tai suunnitella työnkulkuja uudelleen operatiivisten kustannusten hallitsemiseksi ja hyvän käyttäjäkokemuksen varmistamiseksi.
*   **Luottamus, turvallisuus ja vaatimustenmukaisuus**: Monissa sovelluksissa on tärkeää varmistaa, että agentit toimivat turvallisesti ja eettisesti. Havainnointi tarjoaa auditointijäljen agentin toimista ja päätöksistä. Tätä voidaan käyttää havaitsemaan ja lieventämään ongelmia, kuten kehotteiden manipulointia, haitallisen sisällön tuottamista tai henkilötietojen väärinkäsittelyä. Esimerkiksi jälkiä voidaan tarkastella ymmärtääkseen, miksi agentti antoi tietyn vastauksen tai käytti tiettyä työkalua.
*   **Jatkuvat parannussilmukat**: Havainnointidata on iteratiivisen kehitysprosessin perusta. Seuraamalla, miten agentit toimivat todellisessa maailmassa, tiimit voivat tunnistaa parannuskohteita, kerätä dataa mallien hienosäätöä varten ja validoida muutosten vaikutuksia. Tämä luo palautesilmukan, jossa tuotannon havainnoista saatu tieto ohjaa offline-kokeiluja ja -parannuksia, mikä johtaa agenttien suorituskyvyn asteittaiseen paranemiseen.

## Keskeiset seurattavat mittarit

Agenttien käyttäytymisen seuraamiseksi ja ymmärtämiseksi on tärkeää seurata erilaisia mittareita ja signaaleja. Vaikka tietyt mittarit voivat vaihdella agentin tarkoituksen mukaan, jotkut ovat yleisesti tärkeitä.

Tässä ovat yleisimmät mittarit, joita havainnointityökalut seuraavat:

**Viive:** Kuinka nopeasti agentti vastaa? Pitkät odotusajat heikentävät käyttäjäkokemusta. Viivettä tulisi mitata tehtävien ja yksittäisten vaiheiden osalta jäljittämällä agentin suorituksia. Esimerkiksi agentti, joka käyttää 20 sekuntia kaikkiin mallikutsuihin, voisi nopeutua käyttämällä nopeampaa mallia tai suorittamalla mallikutsut rinnakkain.

**Kustannukset:** Mikä on agentin suorituksen kustannus? AI-agentit luottavat LLM-kutsuihin, jotka laskutetaan tokenien perusteella, tai ulkoisiin API:hin. Usein toistuva työkalujen käyttö tai useat kehotteet voivat nopeasti kasvattaa kustannuksia. Esimerkiksi, jos agentti kutsuu LLM:ää viisi kertaa vain marginaalisen laadun parantamiseksi, on arvioitava, ovatko kustannukset perusteltuja, vai voisiko kutsujen määrää vähentää tai käyttää halvempaa mallia. Reaaliaikainen seuranta voi myös auttaa tunnistamaan odottamattomia piikkejä (esim. virheitä, jotka aiheuttavat liiallisia API-silmukoita).

**Pyyntövirheet:** Kuinka monta pyyntöä agentti epäonnistui? Tämä voi sisältää API-virheitä tai epäonnistuneita työkalukutsuja. Agentin tekemisestä kestävämpi tuotannossa voi auttaa esimerkiksi varajärjestelmien tai uudelleenkokeilujen asettaminen. Esim. jos LLM-palveluntarjoaja A on alhaalla, voit vaihtaa varajärjestelmänä LLM-palveluntarjoajaan B.

**Käyttäjäpalaute:** Suoran käyttäjäarvioinnin toteuttaminen tarjoaa arvokasta tietoa. Tämä voi sisältää eksplisiittisiä arvioita (👍peukku ylös/👎alas, ⭐1-5 tähteä) tai tekstikommentteja. Jatkuva negatiivinen palaute on merkki siitä, että agentti ei toimi odotetusti.

**Epäsuora käyttäjäpalaute:** Käyttäjien käyttäytyminen tarjoaa epäsuoraa palautetta, vaikka eksplisiittisiä arvioita ei annettaisikaan. Tämä voi sisältää välittömän kysymyksen uudelleenmuotoilun, toistuvat kyselyt tai uudelleenkokeilupainikkeen klikkauksen. Esim. jos huomaat, että käyttäjät kysyvät toistuvasti samaa kysymystä, tämä on merkki siitä, että agentti ei toimi odotetusti.

**Tarkkuus:** Kuinka usein agentti tuottaa oikeita tai toivottuja tuloksia? Tarkkuuden määritelmät vaihtelevat (esim. ongelmanratkaisun oikeellisuus, tiedonhankinnan tarkkuus, käyttäjätyytyväisyys). Ensimmäinen askel on määritellä, mitä menestys tarkoittaa agentillesi. Voit seurata tarkkuutta automatisoitujen tarkistusten, arviointipisteiden tai tehtävän suoritusmerkintöjen avulla. Esimerkiksi jälkien merkitseminen "onnistuneiksi" tai "epäonnistuneiksi".

**Automaattiset arviointimittarit:** Voit myös asettaa automaattisia arviointeja. Esimerkiksi voit käyttää LLM:ää arvioimaan agentin tuotosta, esim. onko se hyödyllinen, tarkka tai ei. On myös useita avoimen lähdekoodin kirjastoja, jotka auttavat arvioimaan agentin eri osa-alueita. Esim. [RAGAS](https://docs.ragas.io/) RAG-agenteille tai [LLM Guard](https://llm-guard.com/) haitallisen kielen tai kehotemanipulaation havaitsemiseen.

Käytännössä näiden mittareiden yhdistelmä antaa parhaan kattavuuden AI-agentin tilasta. Tämän luvun [esimerkkivihkossa](code_samples/10_autogen_evaluation.ipynb) näytämme, miltä nämä mittarit näyttävät todellisissa esimerkeissä, mutta ensin opimme, miltä tyypillinen arviointityönkulku näyttää.

## Instrumentoi agenttisi

Jälkientiedon keräämiseksi sinun on instrumentoitava koodisi. Tavoitteena on instrumentoida agenttikoodi tuottamaan jälkiä ja mittareita, jotka voidaan kaapata, käsitellä ja visualisoida havainnointialustalla.

**OpenTelemetry (OTel):** [OpenTelemetry](https://opentelemetry.io/) on noussut teollisuusstandardiksi LLM-havainnoinnissa. Se tarjoaa joukon API:ita, SDK:ita ja työkaluja telemetriatiedon tuottamiseen, keräämiseen ja viemiseen.

On olemassa monia instrumentointikirjastoja, jotka kietovat olemassa olevat agenttikehykset ja tekevät OpenTelemetry-spanien viemisen havainnointityökaluun helpoksi. Alla on esimerkki AutoGen-agentin instrumentoinnista [OpenLit-instrumentointikirjastolla](https://github.com/openlit/openlit):

```python
import openlit

openlit.init(tracer = langfuse._otel_tracer, disable_batch = True)
```

Tämän luvun [esimerkkivihko](code_samples/10_autogen_evaluation.ipynb) näyttää, kuinka instrumentoit AutoGen-agenttisi.

**Manuaalinen spanien luominen:** Vaikka instrumentointikirjastot tarjoavat hyvän perustan, on usein tapauksia, joissa tarvitaan yksityiskohtaisempaa tai mukautettua tietoa. Voit luoda spanit manuaalisesti lisätäksesi mukautettua sovelluslogiikkaa. Vielä tärkeämpää on, että ne voivat rikastaa automaattisesti tai manuaalisesti luotuja spaneja mukautetuilla attribuuteilla (tunnetaan myös nimillä tagit tai metadata). Nämä attribuutit voivat sisältää liiketoimintakohtaista dataa, välilaskelmia tai mitä tahansa kontekstia, joka voi olla hyödyllistä vianetsinnässä tai analyysissä, kuten `user_id`, `session_id` tai `model_version`.

Esimerkki jälkien ja spanien manuaalisesta luomisesta [Langfusen Python SDK:lla](https://langfuse.com/docs/sdk/python/sdk-v3): 

```python
from langfuse import get_client
 
langfuse = get_client()
 
span = langfuse.start_span(name="my-span")
 
span.end()
```

## Agentin arviointi

Havainnointi antaa meille mittareita, mutta arviointi on prosessi, jossa analysoidaan näitä tietoja (ja suoritetaan testejä) sen määrittämiseksi, kuinka hyvin AI-agentti toimii ja miten sitä voidaan parantaa. Toisin sanoen, kun sinulla on jäljet ja mittarit, miten käytät niitä agentin arvioimiseen ja päätösten tekemiseen?

Säännöllinen arviointi on tärkeää, koska AI-agentit ovat usein ei-deterministisiä ja voivat kehittyä (päivitysten tai mallin käyttäytymisen muutosten kautta) – ilman arviointia et tietäisi, toimiiko "älykäs agenttisi" todella hyvin vai onko se taantunut.

AI-agenttien arvioinnit jaetaan kahteen kategoriaan: **offline-arviointi** ja **online-arviointi**. Molemmat ovat arvokkaita ja täydentävät toisiaan. Yleensä aloitetaan offline-arvioinnilla, koska se on vähimmäisvaatimus ennen agentin käyttöönottoa.

### Offline-arviointi

![Dataset-kohteet Langfusessa](https://langfuse.com/images/cookbook/example-autogen-evaluation/example-dataset.png)

Tässä arvioidaan agenttia kontrolloidussa ympäristössä, yleensä testidatasetin avulla, ei live-käyttäjäkyselyillä. Käytät kuratoituja datasettejä, joissa tiedät, mikä on odotettu tulos tai oikea käyttäytyminen, ja suoritat agenttisi niiden avulla.

Esimerkiksi, jos olet rakentanut matemaattisten sanallisten ongelmien ratkaisijan, sinulla saattaa olla [testidatasetti](https://huggingface.co/datasets/gsm8k), jossa on 100 ongelmaa ja tunnetut vastaukset. Offline-arviointi tehdään usein kehityksen aikana (ja se voi olla osa CI/CD-putkia) parannusten tarkistamiseksi tai taantumien estämiseksi. Etuna on, että se on **toistettavissa ja voit saada selkeitä tarkkuusmittareita, koska sinulla on totuudenmukaiset vastaukset**. Voit myös simuloida käyttäjäkyselyitä ja mitata agentin vastauksia ihanteellisia vastauksia vastaan tai käyttää yllä kuvattuja automaattisia mittareita.

Offline-arvioinnin keskeinen haaste on varmistaa, että testidatasetti on kattava ja pysyy ajankohtaisena – agentti voi suoriutua hyvin kiinteästä testisetistä, mutta kohdata hyvin erilaisia kyselyitä tuotannossa. Siksi testisettejä tulisi päivittää uusilla reunatapauksilla ja esimerkeillä, jotka heijastavat todellisia skenaarioita. Pieniä "savutesti"-tapauksia ja laajempia arviointisettejä kannattaa yhdistää: pienet setit nopeisiin tarkistuksiin ja laajemmat laajempiin suorituskykymittareihin.

### Online-arviointi 

![Havainnointimittarien yleiskatsaus](https://langfuse.com/images/cookbook/example-autogen-evaluation/dashboard.png)

Tämä viittaa agentin arviointiin live-ympäristössä, eli todellisessa käytössä tuotannossa. Online-arviointi sisältää agentin suorituskyvyn jatkuvan seurannan todellisissa käyttäjävuorovaikutuksissa ja tulosten analysoinnin.

Esimerkiksi voit seurata onnistumisprosentteja, käyttäjätyytyväisyyspisteitä tai muita mittareita live-liikenteessä. Online-arvioinnin etuna on, että se **paljastaa asioita, joita et ehkä osaa ennakoida laboratorio-olosuhteissa** – voit havaita mallin ajautumista ajan myötä (jos agentin tehokkuus heikkenee syötemallien muuttuessa) ja havaita odottamattomia kyselyitä tai tilanteita, joita ei ollut testidatassasi. Se tarjoaa todellisen kuvan siitä, miten agentti käyttäytyy käytännössä.

Online-arviointi sisältää usein epäsuoran ja suoran käyttäjäpalautteen keräämisen, kuten aiemmin mainittiin, ja mahdollisesti varjotestien tai A/B-testien suorittamisen (jossa agentin uusi versio toimii rinnakkain vanhan kanssa vertailua varten). Haasteena on, että voi olla vaikeaa saada luotettavia merkintöjä tai pisteitä live-vuorovaikutuksista – saatat joutua tukeutumaan käyttäjäpalautteeseen tai alaspäin suuntautuviin mittareihin (esim. klikkasiko käyttäjä tulosta).

### Näiden yhdistäminen

Online- ja offline-arvioinnit eivät ole toisiaan poissulkevia; ne täydentävät toisiaan vahvasti. Online-seurannasta saadut havainnot (esim. uudet käyttäjäkyselytyypit, joissa agentti suoriutuu huonosti) voidaan käyttää offline-testidatasetin laajentamiseen ja parantamiseen. Vastaavasti agentit, jotka suoriutuvat hyvin offline-testeissä, voidaan ottaa luottavaisemmin käyttöön ja seurata online-ympäristössä.

Monet tiimit omaksuvatkin silmukan:

_arvioi offline -> ota käyttöön -> seuraa online -> kerää uusia epäonnistumistapauksia -> lisää offline-datasettiin -> hienosäädä agenttia -> toista_.

## Yleisiä ongelmia

Kun otat AI-agentteja käyttöön tuotannossa, saatat kohdata erilaisia haasteita. Tässä on joitakin yleisiä ongelmia ja mahdollisia ratkaisuja:

| **Ongelma**    | **Mahdollinen ratkaisu**   |
| ------------- | ------------------ |
| AI-agentti ei suorita tehtäviä johdonmukaisesti | - Hienosäädä agentille annettua kehotetta; ole selkeä tavoitteista.<br>- Tunnista, missä tehtävien jakaminen osatehtäviin ja niiden käsittely useilla agenteilla voi auttaa. |
| AI-agentti joutuu jatkuviin silmukoihin  | - Varmista, että sinulla

## Yleisimmät ongelmat ja ratkaisut

Tässä on joitakin yleisiä ongelmia, joita voi ilmetä AI-agenttien tuotantokäytössä, sekä ehdotuksia niiden ratkaisemiseksi:

| **Ongelma** | **Ratkaisu** |
|-------------|--------------|
| Agentti ei tuota odotettuja tuloksia | - Käytä suurempaa mallia, joka on erikoistunut monimutkaisiin päättelytehtäviin. |
| AI-agenttityökalujen kutsut eivät toimi hyvin | - Testaa ja validoi työkalun tuottama sisältö agenttijärjestelmän ulkopuolella.<br>- Hienosäädä määritetyt parametrit, kehotteet ja työkalujen nimet. |
| Moni-agenttijärjestelmä ei toimi johdonmukaisesti | - Hienosäädä jokaiselle agentille annettuja kehotteita, jotta ne ovat tarkkoja ja erottuvat toisistaan.<br>- Rakenna hierarkkinen järjestelmä käyttämällä "reititys"- tai ohjausagenttia, joka määrittää, mikä agentti on oikea tehtävään. |

Monet näistä ongelmista voidaan tunnistaa tehokkaammin, kun käytössä on havaittavuus. Aiemmin käsitellyt jäljitykset ja mittarit auttavat tarkasti paikantamaan, missä agentin työnkulussa ongelmat ilmenevät, mikä tekee virheiden korjaamisesta ja optimoinnista paljon tehokkaampaa.

## Kustannusten hallinta

Tässä on joitakin strategioita AI-agenttien tuotantokäytön kustannusten hallintaan:

**Pienempien mallien käyttö:** Pienet kielimallit (SLM:t) voivat suoriutua hyvin tietyissä agenttikäyttötapauksissa ja vähentää kustannuksia merkittävästi. Kuten aiemmin mainittiin, arviointijärjestelmän rakentaminen suorituskyvyn vertaamiseksi suurempiin malleihin on paras tapa ymmärtää, kuinka hyvin SLM sopii käyttötapaukseesi. Harkitse SLM:ien käyttöä yksinkertaisiin tehtäviin, kuten aikomusten luokitteluun tai parametrien poimintaan, ja varaa suuremmat mallit monimutkaiseen päättelyyn.

**Reititysagentin käyttö:** Samankaltainen strategia on käyttää monenlaisia malleja ja kokoja. Voit käyttää LLM/SLM-mallia tai palvelutonta funktiota reitittämään pyynnöt monimutkaisuuden perusteella sopivimmille malleille. Tämä auttaa vähentämään kustannuksia ja varmistamaan suorituskyvyn oikeissa tehtävissä. Esimerkiksi yksinkertaiset kyselyt voidaan ohjata pienemmille, nopeammille malleille, ja kalliita suuria malleja käytetään vain monimutkaisiin päättelytehtäviin.

**Vastausten välimuisti:** Yleisimpien pyyntöjen ja tehtävien tunnistaminen ja vastausten tarjoaminen ennen kuin ne kulkevat agenttijärjestelmän läpi on hyvä tapa vähentää samankaltaisten pyyntöjen määrää. Voit jopa toteuttaa prosessin, joka tunnistaa, kuinka samankaltainen pyyntö on välimuistissa oleviin pyyntöihin, käyttämällä yksinkertaisempia AI-malleja. Tämä strategia voi merkittävästi vähentää kustannuksia usein kysytyissä kysymyksissä tai yleisissä työnkuluissa.

## Katsotaan, miten tämä toimii käytännössä

[Esimerkkimuistikirjassa tämän osion kohdalla](code_samples/10_autogen_evaluation.ipynb) näemme esimerkkejä siitä, kuinka voimme käyttää havaittavuustyökaluja agenttien seurantaan ja arviointiin.

### Onko sinulla lisää kysymyksiä AI-agenteista tuotantokäytössä?

Liity [Azure AI Foundry Discordiin](https://aka.ms/ai-agents/discord), jossa voit tavata muita oppijoita, osallistua toimistoaikoihin ja saada vastauksia AI-agentteihin liittyviin kysymyksiisi.

## Edellinen oppitunti

[Metakognition suunnittelumalli](../09-metacognition/README.md)

## Seuraava oppitunti

[Agenttiset protokollat](../11-agentic-protocols/README.md)

---

**Vastuuvapauslauseke**:  
Tämä asiakirja on käännetty käyttämällä tekoälypohjaista käännöspalvelua [Co-op Translator](https://github.com/Azure/co-op-translator). Vaikka pyrimme tarkkuuteen, huomioithan, että automaattiset käännökset voivat sisältää virheitä tai epätarkkuuksia. Alkuperäinen asiakirja sen alkuperäisellä kielellä tulisi pitää ensisijaisena lähteenä. Kriittisen tiedon osalta suositellaan ammattimaista ihmiskäännöstä. Emme ole vastuussa väärinkäsityksistä tai virhetulkinnoista, jotka johtuvat tämän käännöksen käytöstä.