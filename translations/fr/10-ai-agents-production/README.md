<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "cdfd0acc8592c1af14f8637833450375",
  "translation_date": "2025-08-28T09:40:40+00:00",
  "source_file": "10-ai-agents-production/README.md",
  "language_code": "fr"
}
-->
# Agents IA en Production : Observabilité et Évaluation

[![Agents IA en Production](../../../translated_images/lesson-10-thumbnail.2b79a30773db093e0b4fb47aaa618069e0afb4745fad4836526cf51df87f9ac9.fr.png)](https://youtu.be/l4TP6IyJxmQ?si=reGOyeqjxFevyDq9)

Lorsque les agents IA passent de prototypes expérimentaux à des applications réelles, il devient essentiel de comprendre leur comportement, de surveiller leurs performances et d'évaluer systématiquement leurs résultats.

## Objectifs d'apprentissage

À la fin de cette leçon, vous saurez comment/comprendre :
- Les concepts fondamentaux de l'observabilité et de l'évaluation des agents
- Les techniques pour améliorer les performances, les coûts et l'efficacité des agents
- Ce qu'il faut évaluer et comment évaluer vos agents IA de manière systématique
- Comment maîtriser les coûts lors du déploiement des agents IA en production
- Comment instrumenter les agents construits avec AutoGen

L'objectif est de vous fournir les connaissances nécessaires pour transformer vos agents "boîte noire" en systèmes transparents, gérables et fiables.

_**Remarque :** Il est important de déployer des agents IA sûrs et dignes de confiance. Consultez également la leçon [Construire des Agents IA Fiables](./06-building-trustworthy-agents/README.md)._

## Traces et Spans

Les outils d'observabilité tels que [Langfuse](https://langfuse.com/) ou [Azure AI Foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/what-is-azure-ai-foundry) représentent généralement les exécutions des agents sous forme de traces et de spans.

- **Trace** : représente une tâche complète de l'agent du début à la fin (comme le traitement d'une requête utilisateur).
- **Spans** : sont des étapes individuelles au sein de la trace (comme l'appel à un modèle de langage ou la récupération de données).

![Arbre de trace dans Langfuse](https://langfuse.com/images/cookbook/example-autogen-evaluation/trace-tree.png)

Sans observabilité, un agent IA peut ressembler à une "boîte noire" – son état interne et son raisonnement sont opaques, ce qui rend difficile le diagnostic des problèmes ou l'optimisation des performances. Avec l'observabilité, les agents deviennent des "boîtes de verre", offrant une transparence essentielle pour instaurer la confiance et garantir qu'ils fonctionnent comme prévu.

## Pourquoi l'observabilité est essentielle en environnement de production

Le passage des agents IA en environnement de production introduit un nouvel ensemble de défis et d'exigences. L'observabilité n'est plus un "plus", mais une capacité essentielle :

*   **Débogage et analyse des causes profondes** : Lorsqu'un agent échoue ou produit un résultat inattendu, les outils d'observabilité fournissent les traces nécessaires pour identifier la source de l'erreur. Cela est particulièrement important pour les agents complexes qui peuvent impliquer plusieurs appels LLM, interactions avec des outils et logique conditionnelle.
*   **Gestion de la latence et des coûts** : Les agents IA s'appuient souvent sur des LLM et d'autres API externes facturées par token ou par appel. L'observabilité permet de suivre précisément ces appels, aidant à identifier les opérations excessivement lentes ou coûteuses. Cela permet aux équipes d'optimiser les prompts, de choisir des modèles plus efficaces ou de repenser les workflows pour maîtriser les coûts opérationnels et garantir une bonne expérience utilisateur.
*   **Confiance, sécurité et conformité** : Dans de nombreuses applications, il est important de garantir que les agents se comportent de manière sûre et éthique. L'observabilité fournit une piste d'audit des actions et décisions des agents. Cela peut être utilisé pour détecter et atténuer des problèmes tels que l'injection de prompts, la génération de contenu nuisible ou la mauvaise gestion des informations personnelles identifiables (PII). Par exemple, vous pouvez examiner les traces pour comprendre pourquoi un agent a fourni une certaine réponse ou utilisé un outil spécifique.
*   **Boucles d'amélioration continue** : Les données d'observabilité constituent la base d'un processus de développement itératif. En surveillant les performances des agents dans le monde réel, les équipes peuvent identifier des domaines à améliorer, collecter des données pour affiner les modèles et valider l'impact des changements. Cela crée une boucle de rétroaction où les informations de production issues de l'évaluation en ligne alimentent l'expérimentation et le raffinement hors ligne, conduisant à des performances d'agent progressivement meilleures.

## Indicateurs clés à suivre

Pour surveiller et comprendre le comportement des agents, il est important de suivre une gamme de métriques et de signaux. Bien que les métriques spécifiques puissent varier en fonction de l'objectif de l'agent, certaines sont universellement importantes.

Voici quelques-unes des métriques les plus courantes suivies par les outils d'observabilité :

**Latence :** Quelle est la rapidité de réponse de l'agent ? Des temps d'attente longs nuisent à l'expérience utilisateur. Vous devriez mesurer la latence des tâches et des étapes individuelles en traçant les exécutions des agents. Par exemple, un agent qui prend 20 secondes pour tous les appels de modèle pourrait être accéléré en utilisant un modèle plus rapide ou en exécutant les appels de modèle en parallèle.

**Coûts :** Quel est le coût par exécution d'agent ? Les agents IA s'appuient sur des appels LLM facturés par token ou des API externes. Une utilisation fréquente des outils ou de multiples prompts peut rapidement augmenter les coûts. Par exemple, si un agent appelle un LLM cinq fois pour une amélioration marginale de la qualité, vous devez évaluer si le coût est justifié ou si vous pourriez réduire le nombre d'appels ou utiliser un modèle moins cher. Une surveillance en temps réel peut également aider à identifier des pics inattendus (par exemple, des bugs provoquant des boucles API excessives).

**Erreurs de requêtes :** Combien de requêtes l'agent a-t-il échoué ? Cela peut inclure des erreurs d'API ou des appels d'outils échoués. Pour rendre votre agent plus robuste en production, vous pouvez alors configurer des mécanismes de secours ou des tentatives de reprise. Par exemple, si le fournisseur LLM A est hors service, vous passez au fournisseur LLM B en tant que solution de secours.

**Retour utilisateur :** Mettre en place des évaluations directes des utilisateurs fournit des informations précieuses. Cela peut inclure des évaluations explicites (👍pouce levé/👎baissé, ⭐1-5 étoiles) ou des commentaires textuels. Des retours négatifs constants devraient vous alerter, car cela indique que l'agent ne fonctionne pas comme prévu.

**Retour utilisateur implicite :** Les comportements des utilisateurs fournissent des retours indirects même sans évaluations explicites. Cela peut inclure une reformulation immédiate de la question, des requêtes répétées ou le clic sur un bouton de réessai. Par exemple, si vous constatez que les utilisateurs posent plusieurs fois la même question, cela indique que l'agent ne fonctionne pas comme prévu.

**Précision :** À quelle fréquence l'agent produit-il des résultats corrects ou souhaitables ? Les définitions de précision varient (par exemple, exactitude dans la résolution de problèmes, précision dans la récupération d'informations, satisfaction des utilisateurs). La première étape consiste à définir ce à quoi ressemble le succès pour votre agent. Vous pouvez suivre la précision via des vérifications automatisées, des scores d'évaluation ou des étiquettes de complétion de tâches. Par exemple, marquer les traces comme "réussies" ou "échouées".

**Métriques d'évaluation automatisée :** Vous pouvez également configurer des évaluations automatisées. Par exemple, vous pouvez utiliser un LLM pour noter la sortie de l'agent, par exemple si elle est utile, précise ou non. Il existe également plusieurs bibliothèques open source qui vous aident à évaluer différents aspects de l'agent. Par exemple, [RAGAS](https://docs.ragas.io/) pour les agents RAG ou [LLM Guard](https://llm-guard.com/) pour détecter le langage nuisible ou l'injection de prompts.

En pratique, une combinaison de ces métriques offre la meilleure couverture de la santé d'un agent IA. Dans le [notebook d'exemple](code_samples/10_autogen_evaluation.ipynb) de ce chapitre, nous vous montrerons à quoi ressemblent ces métriques dans des exemples réels, mais d'abord, nous apprendrons à quoi ressemble un workflow d'évaluation typique.

## Instrumenter votre Agent

Pour collecter des données de traçage, vous devrez instrumenter votre code. L'objectif est d'instrumenter le code de l'agent pour émettre des traces et des métriques qui peuvent être capturées, traitées et visualisées par une plateforme d'observabilité.

**OpenTelemetry (OTel) :** [OpenTelemetry](https://opentelemetry.io/) est devenu une norme industrielle pour l'observabilité des LLM. Il fournit un ensemble d'API, de SDK et d'outils pour générer, collecter et exporter des données de télémétrie.

De nombreuses bibliothèques d'instrumentation enveloppent les frameworks d'agents existants et facilitent l'exportation des spans OpenTelemetry vers un outil d'observabilité. Voici un exemple d'instrumentation d'un agent AutoGen avec la bibliothèque d'instrumentation [OpenLit](https://github.com/openlit/openlit) :

```python
import openlit

openlit.init(tracer = langfuse._otel_tracer, disable_batch = True)
```

Le [notebook d'exemple](code_samples/10_autogen_evaluation.ipynb) de ce chapitre démontrera comment instrumenter votre agent AutoGen.

**Création manuelle de spans :** Bien que les bibliothèques d'instrumentation fournissent une bonne base, il existe souvent des cas où des informations plus détaillées ou personnalisées sont nécessaires. Vous pouvez créer manuellement des spans pour ajouter une logique d'application personnalisée. Plus important encore, ils peuvent enrichir les spans créés automatiquement ou manuellement avec des attributs personnalisés (également appelés tags ou métadonnées). Ces attributs peuvent inclure des données spécifiques à l'entreprise, des calculs intermédiaires ou tout contexte utile pour le débogage ou l'analyse, comme `user_id`, `session_id` ou `model_version`.

Exemple de création manuelle de traces et de spans avec le [Langfuse Python SDK](https://langfuse.com/docs/sdk/python/sdk-v3) :

```python
from langfuse import get_client
 
langfuse = get_client()
 
span = langfuse.start_span(name="my-span")
 
span.end()
```

## Évaluation des Agents

L'observabilité nous fournit des métriques, mais l'évaluation est le processus d'analyse de ces données (et de réalisation de tests) pour déterminer dans quelle mesure un agent IA fonctionne et comment il peut être amélioré. En d'autres termes, une fois que vous avez ces traces et métriques, comment les utilisez-vous pour juger l'agent et prendre des décisions ?

L'évaluation régulière est importante car les agents IA sont souvent non déterministes et peuvent évoluer (par des mises à jour ou des dérives de comportement du modèle) – sans évaluation, vous ne sauriez pas si votre "agent intelligent" fait bien son travail ou s'il a régressé.

Il existe deux catégories d'évaluations pour les agents IA : **évaluation hors ligne** et **évaluation en ligne**. Les deux sont précieuses et se complètent. Nous commençons généralement par l'évaluation hors ligne, car c'est l'étape minimale nécessaire avant de déployer un agent.

### Évaluation Hors Ligne

![Éléments de dataset dans Langfuse](https://langfuse.com/images/cookbook/example-autogen-evaluation/example-dataset.png)

Cela consiste à évaluer l'agent dans un environnement contrôlé, généralement à l'aide de jeux de données de test, et non de requêtes d'utilisateurs en direct. Vous utilisez des jeux de données sélectionnés où vous connaissez le résultat attendu ou le comportement correct, puis vous exécutez votre agent sur ceux-ci.

Par exemple, si vous avez construit un agent pour résoudre des problèmes mathématiques, vous pourriez avoir un [jeu de données de test](https://huggingface.co/datasets/gsm8k) de 100 problèmes avec des réponses connues. L'évaluation hors ligne est souvent réalisée pendant le développement (et peut faire partie des pipelines CI/CD) pour vérifier les améliorations ou éviter les régressions. L'avantage est qu'elle est **répétable et vous pouvez obtenir des métriques de précision claires puisque vous avez une vérité terrain**. Vous pourriez également simuler des requêtes d'utilisateurs et mesurer les réponses de l'agent par rapport à des réponses idéales ou utiliser des métriques automatisées comme décrit ci-dessus.

Le principal défi de l'évaluation hors ligne est de s'assurer que votre jeu de données de test est complet et reste pertinent – l'agent pourrait bien fonctionner sur un ensemble de test fixe mais rencontrer des requêtes très différentes en production. Par conséquent, vous devriez maintenir les ensembles de test à jour avec de nouveaux cas limites et exemples reflétant des scénarios réels. Un mélange de petits cas de "test rapide" et de plus grands ensembles d'évaluation est utile : petits ensembles pour des vérifications rapides et plus grands pour des métriques de performance plus larges.

### Évaluation En Ligne

![Vue d'ensemble des métriques d'observabilité](https://langfuse.com/images/cookbook/example-autogen-evaluation/dashboard.png)

Cela consiste à évaluer l'agent dans un environnement réel, c'est-à-dire pendant son utilisation en production. L'évaluation en ligne implique de surveiller les performances de l'agent sur des interactions réelles avec les utilisateurs et d'analyser les résultats en continu.

Par exemple, vous pourriez suivre les taux de réussite, les scores de satisfaction des utilisateurs ou d'autres métriques sur le trafic en direct. L'avantage de l'évaluation en ligne est qu'elle **capture des éléments que vous pourriez ne pas anticiper en laboratoire** – vous pouvez observer la dérive du modèle au fil du temps (si l'efficacité de l'agent diminue à mesure que les schémas d'entrée évoluent) et détecter des requêtes ou situations inattendues qui n'étaient pas dans vos données de test. Elle fournit une image fidèle du comportement de l'agent dans la réalité.

L'évaluation en ligne implique souvent de collecter des retours implicites et explicites des utilisateurs, comme discuté, et éventuellement de réaliser des tests en parallèle ou des tests A/B (où une nouvelle version de l'agent fonctionne en parallèle pour comparer avec l'ancienne). Le défi est qu'il peut être difficile d'obtenir des étiquettes ou des scores fiables pour les interactions en direct – vous pourriez vous appuyer sur les retours des utilisateurs ou des métriques en aval (comme si l'utilisateur a cliqué sur le résultat).

### Combiner les deux

Les évaluations en ligne et hors ligne ne sont pas mutuellement exclusives ; elles se complètent fortement. Les informations issues de la surveillance en ligne (par exemple, de nouveaux types de requêtes utilisateur où l'agent fonctionne mal) peuvent être utilisées pour enrichir et améliorer les jeux de données de test hors ligne. Inversement, les agents qui fonctionnent bien lors des tests hors ligne peuvent ensuite être déployés et surveillés en ligne avec plus de confiance.

En fait, de nombreuses équipes adoptent une boucle :

_évaluer hors ligne -> déployer -> surveiller en ligne -> collecter de nouveaux cas d'échec -> ajouter au jeu de données hors ligne -> affiner l'agent -> répéter_.

## Problèmes Courants

Lorsque vous déployez des agents IA en production, vous pouvez rencontrer divers défis. Voici quelques problèmes courants et leurs solutions potentielles :

| **Problème**    | **Solution Potentielle**   |
| ------------- | ------------------ |
| L'agent IA n'exécute pas les tâches de manière cohérente | - Affinez le prompt donné à l'agent IA ; soyez clair sur les objectifs.<br>- Identifiez où diviser les tâches en sous-tâches et les confier à plusieurs agents peut aider. |
| L'agent IA entre dans des boucles continues  | - Assurez-vous d'avoir des termes et conditions de terminaison clairs pour que l'agent sache quand arrêter le processus. |

- Pour les tâches complexes nécessitant un raisonnement et une planification, utilisez un modèle plus grand spécialisé dans les tâches de raisonnement. |
| Les appels d'outils d'agent AI ne fonctionnent pas bien   | - Testez et validez les résultats des outils en dehors du système d'agent.<br>- Affinez les paramètres définis, les invites et les noms des outils.  |
| Le système multi-agents ne fonctionne pas de manière cohérente | - Affinez les invites données à chaque agent pour garantir qu'elles soient spécifiques et distinctes les unes des autres.<br>- Construisez un système hiérarchique en utilisant un agent "routeur" ou contrôleur pour déterminer quel agent est le plus approprié. |

Beaucoup de ces problèmes peuvent être identifiés plus efficacement avec une observabilité en place. Les traces et métriques que nous avons discutées précédemment aident à localiser précisément où dans le flux de travail de l'agent les problèmes surviennent, rendant le débogage et l'optimisation beaucoup plus efficaces.

## Gestion des coûts

Voici quelques stratégies pour gérer les coûts liés au déploiement des agents AI en production :

**Utilisation de modèles plus petits :** Les Small Language Models (SLMs) peuvent bien fonctionner pour certains cas d'utilisation agentiques et réduiront considérablement les coûts. Comme mentionné précédemment, construire un système d'évaluation pour déterminer et comparer les performances par rapport aux modèles plus grands est le meilleur moyen de comprendre à quel point un SLM peut répondre à votre cas d'utilisation. Envisagez d'utiliser des SLMs pour des tâches plus simples comme la classification d'intentions ou l'extraction de paramètres, tout en réservant les modèles plus grands pour les tâches de raisonnement complexes.

**Utilisation d'un modèle routeur :** Une stratégie similaire consiste à utiliser une diversité de modèles et de tailles. Vous pouvez utiliser un LLM/SLM ou une fonction sans serveur pour router les requêtes en fonction de leur complexité vers les modèles les plus adaptés. Cela permettra également de réduire les coûts tout en garantissant des performances optimales pour les tâches appropriées. Par exemple, routez les requêtes simples vers des modèles plus petits et rapides, et utilisez uniquement des modèles grands et coûteux pour les tâches de raisonnement complexes.

**Mise en cache des réponses :** Identifier les requêtes et tâches courantes et fournir les réponses avant qu'elles ne passent par votre système agentique est une bonne manière de réduire le volume de requêtes similaires. Vous pouvez même mettre en place un flux pour identifier à quel point une requête est similaire à vos requêtes mises en cache en utilisant des modèles AI plus basiques. Cette stratégie peut réduire significativement les coûts pour les questions fréquemment posées ou les flux de travail courants.

## Voyons comment cela fonctionne en pratique

Dans le [notebook d'exemple de cette section](code_samples/10_autogen_evaluation.ipynb), nous verrons des exemples de l'utilisation des outils d'observabilité pour surveiller et évaluer nos agents.


### Vous avez d'autres questions sur les agents AI en production ?

Rejoignez le [Discord Azure AI Foundry](https://aka.ms/ai-agents/discord) pour rencontrer d'autres apprenants, assister à des heures de bureau et obtenir des réponses à vos questions sur les agents AI.

## Leçon précédente

[Design Pattern de Métacognition](../09-metacognition/README.md)

## Leçon suivante

[Protocoles Agentiques](../11-agentic-protocols/README.md)

---

**Avertissement** :  
Ce document a été traduit à l'aide du service de traduction automatique [Co-op Translator](https://github.com/Azure/co-op-translator). Bien que nous nous efforcions d'assurer l'exactitude, veuillez noter que les traductions automatisées peuvent contenir des erreurs ou des inexactitudes. Le document original dans sa langue d'origine doit être considéré comme la source faisant autorité. Pour des informations critiques, il est recommandé de recourir à une traduction professionnelle réalisée par un humain. Nous déclinons toute responsabilité en cas de malentendus ou d'interprétations erronées résultant de l'utilisation de cette traduction.