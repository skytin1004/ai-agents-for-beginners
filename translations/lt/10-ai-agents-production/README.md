<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "cdfd0acc8592c1af14f8637833450375",
  "translation_date": "2025-08-30T14:36:35+00:00",
  "source_file": "10-ai-agents-production/README.md",
  "language_code": "lt"
}
-->
# AI agentai gamyboje: stebėjimas ir vertinimas

[![AI agentai gamyboje](../../../translated_images/lesson-10-thumbnail.2b79a30773db093e0b4fb47aaa618069e0afb4745fad4836526cf51df87f9ac9.lt.png)](https://youtu.be/l4TP6IyJxmQ?si=reGOyeqjxFevyDq9)

Kai AI agentai pereina iš eksperimentinių prototipų į realias programas, tampa svarbu suprasti jų elgesį, stebėti jų veikimą ir sistemingai vertinti jų rezultatus.

## Mokymosi tikslai

Baigę šią pamoką, jūs sužinosite, kaip:
- Suprasti pagrindines agentų stebėjimo ir vertinimo sąvokas
- Naudoti technikas, skirtas agentų veikimui, kaštams ir efektyvumui gerinti
- Sistemingai vertinti savo AI agentus
- Valdyti kaštus, diegiant AI agentus gamyboje
- Instrumentuoti agentus, sukurtus naudojant AutoGen

Tikslas – suteikti jums žinių, kaip paversti savo „juodosios dėžės“ agentus skaidriomis, valdomomis ir patikimomis sistemomis.

_**Pastaba:** Svarbu diegti saugius ir patikimus AI agentus. Peržiūrėkite pamoką [Kaip kurti patikimus AI agentus](./06-building-trustworthy-agents/README.md)._

## Traces ir Spans

Stebėjimo įrankiai, tokie kaip [Langfuse](https://langfuse.com/) ar [Azure AI Foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/what-is-azure-ai-foundry), paprastai atvaizduoja agentų veikimą kaip traces ir spans.

- **Trace** reiškia visą agento užduotį nuo pradžios iki pabaigos (pvz., vartotojo užklausos apdorojimą).
- **Spans** yra atskiri žingsniai trace viduje (pvz., kalbos modelio iškvietimas ar duomenų gavimas).

![Trace medis Langfuse](https://langfuse.com/images/cookbook/example-autogen-evaluation/trace-tree.png)

Be stebėjimo, AI agentas gali atrodyti kaip „juodoji dėžė“ – jo vidinė būsena ir logika yra neaiškios, todėl sunku diagnozuoti problemas ar optimizuoti veikimą. Su stebėjimu agentai tampa „stiklinėmis dėžėmis“, suteikiančiomis skaidrumą, kuris yra būtinas pasitikėjimui kurti ir užtikrinti, kad jie veiktų taip, kaip numatyta.

## Kodėl stebėjimas svarbus gamybos aplinkoje

AI agentų perkėlimas į gamybos aplinką sukelia naujų iššūkių ir reikalavimų. Stebėjimas tampa nebe „maloniu priedu“, o būtina funkcija:

*   **Derinimas ir priežasties analizė:** Kai agentas sugenda arba pateikia netikėtą rezultatą, stebėjimo įrankiai pateikia traces, leidžiančius nustatyti klaidos šaltinį. Tai ypač svarbu sudėtingiems agentams, kurie gali apimti kelis LLM iškvietimus, įrankių sąveikas ir sąlyginę logiką.
*   **Vėlavimo ir kaštų valdymas:** AI agentai dažnai remiasi LLM ir kitomis išorinėmis API, kurios apmokestinamos už žodį ar iškvietimą. Stebėjimas leidžia tiksliai sekti šiuos iškvietimus, padedant nustatyti operacijas, kurios yra pernelyg lėtos ar brangios. Tai leidžia komandoms optimizuoti užklausas, pasirinkti efektyvesnius modelius ar pertvarkyti darbo eigas, kad būtų valdomi veiklos kaštai ir užtikrinta gera vartotojo patirtis.
*   **Pasitikėjimas, saugumas ir atitiktis:** Daugelyje programų svarbu užtikrinti, kad agentai elgtųsi saugiai ir etiškai. Stebėjimas suteikia agentų veiksmų ir sprendimų audito pėdsaką. Tai gali būti naudojama aptikti ir spręsti tokias problemas kaip užklausų injekcija, žalingo turinio generavimas ar netinkamas asmens duomenų tvarkymas. Pavyzdžiui, galite peržiūrėti traces, kad suprastumėte, kodėl agentas pateikė tam tikrą atsakymą ar naudojo konkretų įrankį.
*   **Nuolatinio tobulinimo ciklai:** Stebėjimo duomenys yra iteracinio kūrimo proceso pagrindas. Stebėdami, kaip agentai veikia realiame pasaulyje, komandos gali nustatyti tobulinimo sritis, surinkti duomenis modelių tobulinimui ir patikrinti pakeitimų poveikį. Tai sukuria grįžtamojo ryšio ciklą, kai gamybos įžvalgos iš internetinio vertinimo informuoja neprisijungus atliekamus eksperimentus ir tobulinimą, taip palaipsniui gerinant agentų veikimą.

## Pagrindiniai stebimi rodikliai

Norint stebėti ir suprasti agentų elgesį, reikia sekti įvairius rodiklius ir signalus. Nors konkretūs rodikliai gali skirtis priklausomai nuo agento paskirties, kai kurie yra universaliai svarbūs.

Štai keletas dažniausiai stebimų rodiklių:

**Vėlavimas:** Kaip greitai agentas reaguoja? Ilgi laukimo laikai neigiamai veikia vartotojo patirtį. Turėtumėte matuoti užduočių ir atskirų žingsnių vėlavimą, sekdami agentų veikimą. Pavyzdžiui, agentas, kuris užtrunka 20 sekundžių visiems modelio iškvietimams, galėtų būti pagreitintas naudojant greitesnį modelį arba vykdant modelio iškvietimus lygiagrečiai.

**Kaštai:** Kokia yra agento veikimo kaina? AI agentai remiasi LLM iškvietimais, apmokestinamais už žodį, arba išorinėmis API. Dažnas įrankių naudojimas ar keli užklausų rinkiniai gali greitai padidinti kaštus. Pavyzdžiui, jei agentas penkis kartus iškviečia LLM, kad šiek tiek pagerintų kokybę, turite įvertinti, ar kaštai yra pagrįsti, ar galite sumažinti iškvietimų skaičių arba naudoti pigesnį modelį. Realaus laiko stebėjimas taip pat padeda nustatyti netikėtus šuolius (pvz., klaidas, sukeliančias per daug API ciklų).

**Užklausų klaidos:** Kiek užklausų agentas nesugebėjo įvykdyti? Tai gali apimti API klaidas ar nepavykusius įrankių iškvietimus. Kad agentas būtų atsparesnis gamyboje, galite nustatyti atsarginius sprendimus ar pakartotinius bandymus. Pvz., jei LLM tiekėjas A neveikia, galite pereiti prie LLM tiekėjo B kaip atsarginio varianto.

**Vartotojų atsiliepimai:** Tiesioginiai vartotojų vertinimai suteikia vertingų įžvalgų. Tai gali būti aiškūs įvertinimai (👍teigiamas/👎neigiamas, ⭐1-5 žvaigždutės) arba tekstiniai komentarai. Nuolatiniai neigiami atsiliepimai turėtų būti signalas, kad agentas neveikia taip, kaip tikėtasi.

**Netiesioginiai vartotojų atsiliepimai:** Vartotojų elgesys suteikia netiesioginių atsiliepimų net ir be aiškių vertinimų. Tai gali būti klausimų perrašymas, pakartotinės užklausos ar mygtuko „bandyti dar kartą“ paspaudimas. Pvz., jei matote, kad vartotojai nuolat užduoda tą patį klausimą, tai yra ženklas, kad agentas neveikia taip, kaip tikėtasi.

**Tikslumas:** Kaip dažnai agentas pateikia teisingus ar pageidaujamus rezultatus? Tikslumo apibrėžimai gali skirtis (pvz., problemų sprendimo tikslumas, informacijos paieškos tikslumas, vartotojų pasitenkinimas). Pirmas žingsnis – apibrėžti, kas yra sėkmė jūsų agentui. Tikslumą galite sekti naudodami automatinius patikrinimus, vertinimo balus ar užduočių užbaigimo žymes. Pavyzdžiui, traces gali būti pažymėtos kaip „pavyko“ arba „nepavyko“.

**Automatizuoti vertinimo rodikliai:** Taip pat galite nustatyti automatizuotus vertinimus. Pavyzdžiui, galite naudoti LLM, kad įvertintumėte agento išvestį, pvz., ar ji naudinga, tiksli ar ne. Taip pat yra keletas atvirojo kodo bibliotekų, kurios padeda įvertinti skirtingus agento aspektus, pvz., [RAGAS](https://docs.ragas.io/) RAG agentams arba [LLM Guard](https://llm-guard.com/) žalingos kalbos ar užklausų injekcijos aptikimui.

Praktiškai geriausią agento sveikatos stebėjimą užtikrina šių rodiklių derinys. Šio skyriaus [pavyzdžių užrašinėje](code_samples/10_autogen_evaluation.ipynb) parodysime, kaip šie rodikliai atrodo realiuose pavyzdžiuose, tačiau pirmiausia išmoksime, kaip atrodo tipinis vertinimo darbo srautas.

## Instrumentuokite savo agentą

Norėdami surinkti traces duomenis, turėsite instrumentuoti savo kodą. Tikslas – priversti agento kodą generuoti traces ir rodiklius, kuriuos galima užfiksuoti, apdoroti ir vizualizuoti stebėjimo platformoje.

**OpenTelemetry (OTel):** [OpenTelemetry](https://opentelemetry.io/) tapo pramonės standartu LLM stebėjimui. Jis suteikia API, SDK ir įrankių rinkinį telemetrijos duomenims generuoti, rinkti ir eksportuoti.

Yra daug instrumentavimo bibliotekų, kurios apgaubia esamus agentų karkasus ir leidžia lengvai eksportuoti OpenTelemetry spans į stebėjimo įrankį. Žemiau pateiktas pavyzdys, kaip instrumentuoti AutoGen agentą naudojant [OpenLit instrumentavimo biblioteką](https://github.com/openlit/openlit):

```python
import openlit

openlit.init(tracer = langfuse._otel_tracer, disable_batch = True)
```

Šio skyriaus [pavyzdžių užrašinėje](code_samples/10_autogen_evaluation.ipynb) bus parodyta, kaip instrumentuoti savo AutoGen agentą.

**Rankinis spans kūrimas:** Nors instrumentavimo bibliotekos suteikia gerą pagrindą, dažnai būna atvejų, kai reikia detalesnės ar pritaikytos informacijos. Galite rankiniu būdu kurti spans, kad pridėtumėte pritaikytą programos logiką. Dar svarbiau, kad jie gali praturtinti automatiškai ar rankiniu būdu sukurtus spans pritaikytais atributais (dar vadinamais žymėmis ar metaduomenimis). Šie atributai gali apimti verslo specifinius duomenis, tarpinius skaičiavimus ar bet kokį kontekstą, kuris gali būti naudingas derinimui ar analizei, pvz., `user_id`, `session_id` ar `model_version`.

Pavyzdys, kaip rankiniu būdu kurti traces ir spans naudojant [Langfuse Python SDK](https://langfuse.com/docs/sdk/python/sdk-v3):

```python
from langfuse import get_client
 
langfuse = get_client()
 
span = langfuse.start_span(name="my-span")
 
span.end()
```

## Agentų vertinimas

Stebėjimas suteikia mums rodiklius, tačiau vertinimas yra procesas, kurio metu analizuojami tie duomenys (ir atliekami testai), siekiant nustatyti, kaip gerai veikia AI agentas ir kaip jį galima patobulinti. Kitaip tariant, kai jau turite traces ir rodiklius, kaip juos panaudoti agento vertinimui ir sprendimų priėmimui?

Reguliarus vertinimas yra svarbus, nes AI agentai dažnai yra nedeterministiniai ir gali keistis (dėl atnaujinimų ar modelio elgsenos pokyčių) – be vertinimo jūs nežinotumėte, ar jūsų „protingas agentas“ iš tikrųjų gerai atlieka savo darbą, ar jis pablogėjo.

Yra dvi AI agentų vertinimo kategorijos: **neprisijungus atliekamas vertinimas** ir **prisijungus atliekamas vertinimas**. Abi yra vertingos ir papildo viena kitą. Paprastai pradedame nuo neprisijungus atliekamo vertinimo, nes tai yra minimalus būtinas žingsnis prieš diegiant bet kurį agentą.

### Neprisijungus atliekamas vertinimas

![Duomenų rinkinio elementai Langfuse](https://langfuse.com/images/cookbook/example-autogen-evaluation/example-dataset.png)

Tai apima agento vertinimą kontroliuojamoje aplinkoje, paprastai naudojant testų duomenų rinkinius, o ne tiesiogines vartotojų užklausas. Naudojate kruopščiai parinktus duomenų rinkinius, kuriuose žinote, koks yra tikėtinas rezultatas ar teisingas elgesys, ir tada paleidžiate savo agentą su jais.

Pavyzdžiui, jei sukūrėte matematikos uždavinių agentą, galite turėti [testų duomenų rinkinį](https://huggingface.co/datasets/gsm8k) su 100 uždavinių ir žinomais atsakymais. Neprisijungus atliekamas vertinimas dažnai vykdomas kūrimo metu (ir gali būti CI/CD procesų dalis), siekiant patikrinti patobulinimus ar apsisaugoti nuo pablogėjimų. Privalumas yra tas, kad tai yra **kartotina, ir galite gauti aiškius tikslumo rodiklius, nes turite pagrindinę tiesą**. Taip pat galite imituoti vartotojų užklausas ir matuoti agento atsakymus pagal idealus atsakymus arba naudoti automatizuotus rodiklius, kaip aprašyta aukščiau.

Pagrindinis iššūkis atliekant neprisijungus vertinimą yra užtikrinti, kad jūsų testų duomenų rinkinys būtų išsamus ir išliktų aktualus – agentas gali gerai veikti su fiksuotu testų rinkiniu, tačiau susidurti su labai skirtingomis užklausomis gamyboje. Todėl turėtumėte nuolat atnaujinti testų rinkinius, įtraukdami naujus kraštutinius atvejus ir pavyzdžius, atspindinčius realias situacijas. Naudinga turėti mažų „dūmų testų“ atvejų ir didesnių vertinimo rinkinių derinį: mažus rinkinius greitiems patikrinimams ir didesnius – platesniems veikimo rodikliams.

### Prisijungus atliekamas vertinimas

![Stebėjimo rodiklių apžvalga](https://langfuse.com/images/cookbook/example-autogen-evaluation/dashboard.png)

Tai reiškia agento vertinimą realioje, gyvoje aplinkoje, t. y. faktinio naudojimo gamyboje metu. Prisijungus atliekamas vertinimas apima agento veikimo stebėjimą realiose vartotojų sąveikose ir rezultatų analizę nuolat.

Pavyzdžiui, galite sekti sėkmės rodiklius, vartotojų pasitenkinimo balus ar kitus rodiklius tiesioginės srauto metu. Prisijungus atliekamo vertinimo privalumas yra tas, kad jis **užfiksuoja dalykus, kurių galbūt negalėtumėte numatyti laboratorinėje aplinkoje** – galite stebėti modelio pokyčius laikui bėgant (jei agento efektyvumas mažėja keičiantis įvesties modeliams) ir aptikti netikėtas užklausas ar situacijas, kurių nebuvo jūsų testų duomenyse. Tai suteikia tikrą vaizdą, kaip agentas elgiasi realiame pasaulyje.

Prisijungus atliekamas vertinimas dažnai apima netiesioginių ir tiesioginių vartotojų atsiliepimų rinkimą, kaip aptarta, ir galbūt šešėlinių testų ar A/B testų vykdymą (kai nauja agento versija veikia lygiagrečiai, kad būtų galima palyginti su sena). Iššūkis yra tas, kad gali būti sudėtinga gauti patikimas etiketes ar balus už gyvas sąveikas – galite pasikliauti vartotojų atsiliepimais ar tolesniais rodikliais (pvz., ar vartotojas spustelėjo rezultatą).

### Abiejų derinimas

Prisijungus ir neprisijungus atliekami vertinimai nėra tarpusavyje nesuderinami

- Dėl sudėtingų užduočių, kurioms reikia planavimo ir samprotavimo, naudokite didesnį modelį, pritaikytą samprotavimo užduotims. |
| AI agento įrankių kvietimai neveikia gerai   | - Išbandykite ir patikrinkite įrankio rezultatą už agento sistemos ribų.<br>- Patobulinkite apibrėžtus parametrus, užklausas ir įrankių pavadinimus.  |
| Daugiaagentė sistema neveikia nuosekliai | - Patobulinkite užklausas, pateiktas kiekvienam agentui, kad jos būtų konkrečios ir skirtingos viena nuo kitos.<br>- Sukurkite hierarchinę sistemą, naudodami „maršruto“ arba valdymo agentą, kuris nustatytų, kuris agentas yra tinkamiausias. |

Daugelį šių problemų galima efektyviau nustatyti, jei yra stebėjimo priemonės. Anksčiau aptarti sekimo ir metrikos įrankiai padeda tiksliai nustatyti, kur agento darbo eigoje kyla problemų, todėl derinimas ir optimizavimas tampa daug efektyvesnis.

## Kaštų valdymas

Štai keletas strategijų, kaip valdyti AI agentų diegimo į gamybą kaštus:

**Naudojant mažesnius modelius:** Maži kalbos modeliai (SLM) gali gerai veikti tam tikrose agentinėse užduotyse ir žymiai sumažinti kaštus. Kaip minėta anksčiau, vertinimo sistemos sukūrimas, siekiant nustatyti ir palyginti našumą su didesniais modeliais, yra geriausias būdas suprasti, kaip gerai SLM veiks jūsų atveju. Apsvarstykite galimybę naudoti SLM paprastesnėms užduotims, tokioms kaip ketinimų klasifikavimas ar parametrų ištraukimas, o sudėtingam samprotavimui rezervuokite didesnius modelius.

**Naudojant maršruto modelį:** Panaši strategija yra naudoti įvairius modelius ir jų dydžius. Galite naudoti LLM/SLM arba serverless funkciją, kad nukreiptumėte užklausas pagal sudėtingumą į tinkamiausius modelius. Tai padės sumažinti kaštus, tuo pačiu užtikrinant tinkamą našumą konkrečioms užduotims. Pavyzdžiui, nukreipkite paprastas užklausas į mažesnius, greitesnius modelius, o sudėtingoms samprotavimo užduotims naudokite tik brangius didelius modelius.

**Atsakymų talpyklos naudojimas:** Nustatant dažnas užklausas ir užduotis bei pateikiant atsakymus prieš jiems pereinant per agentinę sistemą, galima sumažinti panašių užklausų apimtį. Galite net įgyvendinti procesą, kuris nustatytų, kiek užklausa panaši į jūsų talpykloje esančias užklausas, naudodami paprastesnius AI modelius. Ši strategija gali žymiai sumažinti kaštus dažnai užduodamiems klausimams ar įprastoms darbo eigoms.

## Pažiūrėkime, kaip tai veikia praktikoje

Šiame [šios skilties pavyzdiniame užrašų knygelėje](code_samples/10_autogen_evaluation.ipynb) pamatysime pavyzdžius, kaip galime naudoti stebėjimo įrankius agentų stebėjimui ir vertinimui.

### Turite daugiau klausimų apie AI agentus gamyboje?

Prisijunkite prie [Azure AI Foundry Discord](https://aka.ms/ai-agents/discord), kad susipažintumėte su kitais besimokančiais, dalyvautumėte konsultacijų valandose ir gautumėte atsakymus į savo klausimus apie AI agentus.

## Ankstesnė pamoka

[Metakognicijos dizaino šablonas](../09-metacognition/README.md)

## Kitoji pamoka

[Agentiniai protokolai](../11-agentic-protocols/README.md)

---

**Atsakomybės apribojimas**:  
Šis dokumentas buvo išverstas naudojant dirbtinio intelekto vertimo paslaugą [Co-op Translator](https://github.com/Azure/co-op-translator). Nors siekiame tikslumo, atkreipiame dėmesį, kad automatiniai vertimai gali turėti klaidų ar netikslumų. Originalus dokumentas jo gimtąja kalba turėtų būti laikomas autoritetingu šaltiniu. Kritinei informacijai rekomenduojama naudoti profesionalų žmogaus vertimą. Mes neprisiimame atsakomybės už nesusipratimus ar klaidingus aiškinimus, kylančius dėl šio vertimo naudojimo.