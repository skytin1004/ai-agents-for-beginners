<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "cdfd0acc8592c1af14f8637833450375",
  "translation_date": "2025-08-29T20:05:48+00:00",
  "source_file": "10-ai-agents-production/README.md",
  "language_code": "hu"
}
-->
# AI ügynökök a gyakorlatban: Megfigyelhetőség és értékelés

[![AI ügynökök a gyakorlatban](../../../translated_images/lesson-10-thumbnail.2b79a30773db093e0b4fb47aaa618069e0afb4745fad4836526cf51df87f9ac9.hu.png)](https://youtu.be/l4TP6IyJxmQ?si=reGOyeqjxFevyDq9)

Ahogy az AI ügynökök kísérleti prototípusokból valós alkalmazásokba lépnek, egyre fontosabbá válik viselkedésük megértése, teljesítményük nyomon követése és kimeneteik rendszerszintű értékelése.

## Tanulási célok

A lecke elvégzése után képes leszel:
- Az ügynökök megfigyelhetőségének és értékelésének alapfogalmait megérteni
- Technikákat alkalmazni az ügynökök teljesítményének, költségeinek és hatékonyságának javítására
- Szisztematikusan értékelni az AI ügynökeidet
- Költségeket kontrollálni, amikor AI ügynököket telepítesz a gyakorlatba
- AutoGen-nel épített ügynököket instrumentálni

A cél az, hogy olyan tudással ruházzunk fel, amely lehetővé teszi, hogy a "fekete doboz" ügynököket átlátható, kezelhető és megbízható rendszerekké alakítsd.

_**Megjegyzés:** Fontos, hogy biztonságos és megbízható AI ügynököket telepíts. Nézd meg a [Megbízható AI ügynökök építése](./06-building-trustworthy-agents/README.md) leckét is._

## Nyomok és szakaszok

A megfigyelhetőségi eszközök, mint például a [Langfuse](https://langfuse.com/) vagy az [Azure AI Foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/what-is-azure-ai-foundry) általában az ügynök futtatásait nyomokként és szakaszokként ábrázolják.

- **Nyom**: Egy teljes ügynökfeladatot képvisel a kezdetektől a végéig (például egy felhasználói kérdés kezelése).
- **Szakaszok**: A nyomon belüli egyedi lépések (például egy nyelvi modell hívása vagy adatok lekérése).

![Nyomfa a Langfuse-ban](https://langfuse.com/images/cookbook/example-autogen-evaluation/trace-tree.png)

Megfigyelhetőség nélkül egy AI ügynök olyan, mint egy "fekete doboz" – belső állapota és érvelése átláthatatlan, ami megnehezíti a problémák diagnosztizálását vagy a teljesítmény optimalizálását. Megfigyelhetőséggel az ügynökök "üveg dobozokká" válnak, átláthatóságot biztosítva, amely elengedhetetlen a bizalom építéséhez és annak biztosításához, hogy az ügynökök a kívánt módon működjenek.

## Miért fontos a megfigyelhetőség a gyakorlatban?

Az AI ügynökök gyakorlatba való átültetése új kihívásokat és követelményeket hoz magával. A megfigyelhetőség már nem "jó, ha van", hanem kritikus képesség:

*   **Hibakeresés és okfeltárás**: Amikor egy ügynök hibázik vagy váratlan kimenetet produkál, a megfigyelhetőségi eszközök biztosítják azokat a nyomokat, amelyekkel azonosítható a hiba forrása. Ez különösen fontos összetett ügynököknél, amelyek több LLM hívást, eszközinterakciót és feltételes logikát tartalmazhatnak.
*   **Késleltetés és költségkezelés**: Az AI ügynökök gyakran támaszkodnak LLM-ekre és más külső API-kra, amelyek tokenenként vagy hívásonként kerülnek számlázásra. A megfigyelhetőség lehetővé teszi ezeknek a hívásoknak a pontos nyomon követését, segítve a lassú vagy drága műveletek azonosítását. Ezáltal a csapatok optimalizálhatják a promptokat, hatékonyabb modelleket választhatnak, vagy áttervezhetik a munkafolyamatokat az operációs költségek kezelésére és a jó felhasználói élmény biztosítására.
*   **Bizalom, biztonság és megfelelőség**: Számos alkalmazásban fontos, hogy az ügynökök biztonságosan és etikusan viselkedjenek. A megfigyelhetőség biztosítja az ügynökök cselekedeteinek és döntéseinek auditálási nyomvonalát. Ez felhasználható olyan problémák észlelésére és enyhítésére, mint a prompt injekció, káros tartalom generálása vagy személyes azonosításra alkalmas információk (PII) helytelen kezelése. Például nyomokat vizsgálhatsz, hogy megértsd, miért adott az ügynök egy adott választ vagy miért használt egy adott eszközt.
*   **Folyamatos fejlesztési ciklusok**: A megfigyelhetőségi adatok az iteratív fejlesztési folyamat alapját képezik. Az ügynökök valós teljesítményének nyomon követésével a csapatok azonosíthatják a fejlesztési területeket, adatokat gyűjthetnek a modellek finomhangolásához, és validálhatják a változtatások hatását. Ez egy visszacsatolási hurkot hoz létre, ahol az online értékelésből származó gyártási betekintések informálják az offline kísérletezést és finomítást, fokozatosan jobb ügynökteljesítményt eredményezve.

## Kulcsfontosságú metrikák nyomon követése

Az ügynök viselkedésének nyomon követéséhez és megértéséhez számos metrikát és jelet kell figyelni. Bár a konkrét metrikák az ügynök céljától függően változhatnak, néhány univerzálisan fontos.

Íme néhány a leggyakoribb metrikák közül, amelyeket a megfigyelhetőségi eszközök monitoroznak:

**Késleltetés:** Milyen gyorsan válaszol az ügynök? A hosszú várakozási idők negatívan befolyásolják a felhasználói élményt. A késleltetést a feladatok és az egyes lépések esetében is mérni kell az ügynök futtatásainak nyomon követésével. Például egy ügynök, amely 20 másodpercet vesz igénybe minden modellhívás esetében, gyorsítható egy gyorsabb modell használatával vagy a modellhívások párhuzamos futtatásával.

**Költségek:** Mennyibe kerül egy ügynök futtatása? Az AI ügynökök LLM hívásokra támaszkodnak, amelyek tokenenként vagy külső API-ként kerülnek számlázásra. Gyakori eszközhasználat vagy több prompt gyorsan növelheti a költségeket. Például, ha egy ügynök ötször hív egy LLM-et a marginális minőségjavítás érdekében, értékelni kell, hogy a költség indokolt-e, vagy csökkenthető-e a hívások száma, illetve használható-e olcsóbb modell. A valós idejű monitorozás segíthet azonosítani a váratlan kiugrásokat is (például hibák, amelyek túlzott API hurkokat okoznak).

**Kérés hibák:** Hány kérést nem sikerült teljesítenie az ügynöknek? Ez magában foglalhatja az API hibákat vagy sikertelen eszközhívásokat. Az ügynököt robusztusabbá teheted ezek ellen a gyakorlatban, ha visszaállításokat vagy újrapróbálkozásokat állítasz be. Például, ha az LLM szolgáltató A nem elérhető, válthatsz az LLM szolgáltató B-re tartalékként.

**Felhasználói visszajelzés:** A közvetlen felhasználói értékelések értékes betekintést nyújtanak. Ez magában foglalhatja az explicit értékeléseket (👍fel/thumbs-up/👎le, ⭐1-5 csillag) vagy szöveges megjegyzéseket. A következetesen negatív visszajelzések figyelmeztető jelzésként szolgálnak, hogy az ügynök nem működik megfelelően.

**Implicit felhasználói visszajelzés:** A felhasználói viselkedések közvetett visszajelzést nyújtanak, még explicit értékelések nélkül is. Ez magában foglalhatja az azonnali kérdés újrafogalmazását, ismételt lekérdezéseket vagy egy újrapróbálkozás gomb megnyomását. Például, ha azt látod, hogy a felhasználók ismételten ugyanazt a kérdést teszik fel, ez annak a jele, hogy az ügynök nem működik megfelelően.

**Pontosság:** Milyen gyakran produkál az ügynök helyes vagy kívánatos kimeneteket? A pontosság definíciói változhatnak (például problémamegoldási helyesség, információkeresési pontosság, felhasználói elégedettség). Az első lépés az, hogy meghatározd, mit jelent a siker az ügynököd számára. A pontosságot automatizált ellenőrzésekkel, értékelési pontszámokkal vagy feladat teljesítési címkékkel követheted. Például nyomokat jelölhetsz "sikeres" vagy "sikertelen" címkékkel.

**Automatizált értékelési metrikák:** Automatizált értékeléseket is beállíthatsz. Például használhatsz egy LLM-et az ügynök kimenetének pontozására, például hogy hasznos-e, pontos-e vagy sem. Számos nyílt forráskódú könyvtár is segít az ügynök különböző aspektusainak pontozásában. Például [RAGAS](https://docs.ragas.io/) RAG ügynökökhöz vagy [LLM Guard](https://llm-guard.com/) káros nyelvezet vagy prompt injekció észlelésére.

A gyakorlatban ezeknek a metrikáknak a kombinációja nyújtja a legjobb lefedettséget az AI ügynök egészségéről. Ebben a fejezetben [példa notebookban](code_samples/10_autogen_evaluation.ipynb) megmutatjuk, hogyan néznek ki ezek a metrikák valós példákban, de először megtanuljuk, hogyan néz ki egy tipikus értékelési munkafolyamat.

## Instrumentáld az ügynöködet

A nyomkövetési adatok gyűjtéséhez instrumentálnod kell a kódodat. Az a cél, hogy az ügynök kódját úgy instrumentáld, hogy nyomokat és metrikákat bocsásson ki, amelyeket egy megfigyelhetőségi platform rögzíthet, feldolgozhat és vizualizálhat.

**OpenTelemetry (OTel):** Az [OpenTelemetry](https://opentelemetry.io/) iparági szabvánnyá vált az LLM megfigyelhetőség terén. API-k, SDK-k és eszközök készletét biztosítja a telemetriai adatok generálásához, gyűjtéséhez és exportálásához.

Számos instrumentációs könyvtár létezik, amelyek meglévő ügynökkeretrendszereket csomagolnak, és megkönnyítik az OpenTelemetry szakaszok exportálását egy megfigyelhetőségi eszközbe. Az alábbiakban egy példa az AutoGen ügynök instrumentálására az [OpenLit instrumentációs könyvtárral](https://github.com/openlit/openlit):

```python
import openlit

openlit.init(tracer = langfuse._otel_tracer, disable_batch = True)
```

A fejezet [példa notebookja](code_samples/10_autogen_evaluation.ipynb) bemutatja, hogyan instrumentálhatod az AutoGen ügynöködet.

**Manuális szakasz létrehozás:** Bár az instrumentációs könyvtárak jó alapot biztosítanak, gyakran előfordulnak olyan esetek, amikor részletesebb vagy egyedi információkra van szükség. Manuálisan is létrehozhatsz szakaszokat, hogy egyedi alkalmazáslogikát adj hozzá. Ennél is fontosabb, hogy gazdagíthatod az automatikusan vagy manuálisan létrehozott szakaszokat egyedi attribútumokkal (más néven címkék vagy metaadatok). Ezek az attribútumok tartalmazhatnak üzleti specifikus adatokat, köztes számításokat vagy bármilyen kontextust, amely hasznos lehet a hibakereséshez vagy elemzéshez, például `user_id`, `session_id` vagy `model_version`.

Példa nyomok és szakaszok manuális létrehozására a [Langfuse Python SDK](https://langfuse.com/docs/sdk/python/sdk-v3) segítségével:

```python
from langfuse import get_client
 
langfuse = get_client()
 
span = langfuse.start_span(name="my-span")
 
span.end()
```

## Ügynök értékelése

A megfigyelhetőség metrikákat ad, de az értékelés az adatok elemzésének folyamata (és tesztek végrehajtása), amely meghatározza, hogy az AI ügynök mennyire teljesít jól, és hogyan lehet javítani. Más szavakkal, ha már megvannak a nyomok és metrikák, hogyan használod őket az ügynök megítélésére és döntések meghozatalára?

A rendszeres értékelés fontos, mert az AI ügynökök gyakran nem determinisztikusak és fejlődhetnek (frissítések vagy modellek viselkedésének eltolódása révén) – értékelés nélkül nem tudnád, hogy az "okos ügynököd" valóban jól végzi-e a munkáját, vagy visszafejlődött.

Az AI ügynökök értékelésének két kategóriája van: **online értékelés** és **offline értékelés**. Mindkettő értékes, és kiegészítik egymást. Általában offline értékeléssel kezdünk, mivel ez a minimum szükséges lépés bármely ügynök telepítése előtt.

### Offline értékelés

![Adatkészlet elemei a Langfuse-ban](https://langfuse.com/images/cookbook/example-autogen-evaluation/example-dataset.png)

Ez az ügynök értékelését jelenti kontrollált környezetben, általában tesztadatkészletek használatával, nem élő felhasználói lekérdezésekkel. Olyan gondosan összeállított adatkészleteket használsz, ahol tudod, mi a várható kimenet vagy helyes viselkedés, majd futtatod az ügynököt ezeken.

Például, ha egy matematikai szöveges problémamegoldó ügynököt építettél, lehet, hogy van egy [tesztadatkészleted](https://huggingface.co/datasets/gsm8k) 100 problémával, amelyeknek ismert válaszai vannak. Az offline értékelést gyakran a fejlesztés során végzik (és része lehet a CI/CD folyamatoknak), hogy ellenőrizzék a fejlesztéseket vagy megakadályozzák a visszafejlődést. Az előnye, hogy **ismételhető, és egyértelmű pontossági metrikákat kaphatsz, mivel van alapigazságod**. Szimulálhatod a felhasználói lekérdezéseket, és mérheted az ügynök válaszait az ideális válaszokhoz képest, vagy használhatod az automatizált metrikákat, ahogy fentebb leírtuk.

Az offline értékelés kulcsfontosságú kihívása az, hogy biztosítsd, hogy a tesztadatkészleted átfogó és releváns maradjon – az ügynök jól teljesíthet egy fix tesztkészleten, de nagyon eltérő lekérdezésekkel találkozhat a gyakorlatban. Ezért a tesztkészleteket frissíteni kell új szélsőséges esetekkel és példákkal, amelyek tükrözik a valós forgatókönyveket​. Egy kis "füstteszt" esetek és nagyobb értékelési készletek keveréke hasznos: kis készletek gyors ellenőrzésekhez és nagyobbak szélesebb teljesítménymetrikákhoz​.

### Online ért

## Problémák kezelése

Az AI ügynökök gyártási környezetben történő telepítése során gyakran előfordulhatnak problémák. Az alábbiakban néhány gyakori probléma és azok megoldási javaslatai találhatók:

| **Probléma** | **Megoldás** |
|--------------|--------------|
| Az ügynök nem ad pontos válaszokat | - Finomhangolja az ügynök által használt modellek paramétereit.<br>- Ellenőrizze, hogy a bemeneti adatok megfelelően vannak-e formázva.<br>- Használjon nagyobb modelleket összetettebb feladatokhoz. |
| Az AI ügynök eszköz-hívásai nem működnek megfelelően | - Tesztelje és validálja az eszközök kimenetét az ügynökrendszeren kívül.<br>- Finomhangolja a meghatározott paramétereket, utasításokat és az eszközök elnevezését. |
| A több ügynökből álló rendszer nem működik következetesen | - Finomhangolja az egyes ügynököknek adott utasításokat, hogy azok specifikusak és egymástól jól elkülöníthetőek legyenek.<br>- Építsen hierarchikus rendszert egy "irányító" vagy vezérlő ügynök segítségével, amely eldönti, melyik ügynök a megfelelő. |

Sok ilyen probléma hatékonyabban azonosítható, ha megfigyelhetőségi eszközök állnak rendelkezésre. Az előzőekben tárgyalt nyomkövetési és metrikai eszközök segítenek pontosan meghatározni, hol lépnek fel problémák az ügynök munkafolyamatában, így a hibakeresés és optimalizálás sokkal hatékonyabbá válik.

## Költségek kezelése

Az AI ügynökök gyártási környezetbe történő telepítésének költségeit az alábbi stratégiákkal lehet kezelni:

**Kisebb modellek használata:** Kis nyelvi modellek (SLM-ek) bizonyos ügynöki feladatok esetében jól teljesíthetnek, és jelentősen csökkenthetik a költségeket. Ahogy korábban említettük, egy értékelési rendszer kiépítése, amely összehasonlítja a teljesítményt a nagyobb modellekkel, a legjobb módja annak, hogy megértsük, mennyire jól teljesít egy SLM az adott feladatban. Fontolja meg SLM-ek használatát egyszerűbb feladatokhoz, például szándékosztályozáshoz vagy paraméterek kinyeréséhez, míg a nagyobb modelleket tartsa fenn összetettebb érvelési feladatokhoz.

**Irányító modell használata:** Egy hasonló stratégia a különböző méretű modellek és funkciók kombinálása. Használhat LLM/SLM modellt vagy szerver nélküli funkciót arra, hogy a kéréseket bonyolultság alapján a legmegfelelőbb modellekhez irányítsa. Ez nemcsak költségeket csökkent, hanem biztosítja a megfelelő teljesítményt az adott feladatokhoz. Például egyszerű lekérdezéseket irányítson kisebb, gyorsabb modellekhez, és csak a drága nagy modelleket használja összetett érvelési feladatokhoz.

**Válaszok gyorsítótárazása:** Azonosítsa a gyakori kéréseket és feladatokat, és biztosítsa a válaszokat, mielőtt azok átmennének az ügynöki rendszeren. Még egy olyan folyamatot is bevezethet, amely azonosítja, mennyire hasonló egy kérés a gyorsítótárban lévő kérésekhez, egyszerűbb AI modellek használatával. Ez a stratégia jelentősen csökkentheti a költségeket gyakran ismétlődő kérdések vagy munkafolyamatok esetében.

## Nézzük meg, hogyan működik ez a gyakorlatban

Az [e szakasz példafüzetében](code_samples/10_autogen_evaluation.ipynb) példákat láthatunk arra, hogyan használhatjuk a megfigyelési eszközöket az ügynökök monitorozására és értékelésére.

### További kérdései vannak az AI ügynökökkel kapcsolatban?

Csatlakozzon az [Azure AI Foundry Discord](https://aka.ms/ai-agents/discord) közösséghez, hogy találkozzon más tanulókkal, részt vegyen konzultációkon, és választ kapjon az AI ügynökökkel kapcsolatos kérdéseire.

## Előző lecke

[Metakogníció tervezési minta](../09-metacognition/README.md)

## Következő lecke

[Ügynöki protokollok](../11-agentic-protocols/README.md)

---

**Felelősség kizárása**:  
Ez a dokumentum az AI fordítási szolgáltatás, a [Co-op Translator](https://github.com/Azure/co-op-translator) segítségével lett lefordítva. Bár törekszünk a pontosságra, kérjük, vegye figyelembe, hogy az automatikus fordítások hibákat vagy pontatlanságokat tartalmazhatnak. Az eredeti dokumentum az eredeti nyelvén tekintendő hiteles forrásnak. Kritikus információk esetén javasolt professzionális emberi fordítást igénybe venni. Nem vállalunk felelősséget semmilyen félreértésért vagy téves értelmezésért, amely a fordítás használatából eredhet.