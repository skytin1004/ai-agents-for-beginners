<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "cdfd0acc8592c1af14f8637833450375",
  "translation_date": "2025-08-29T12:45:53+00:00",
  "source_file": "10-ai-agents-production/README.md",
  "language_code": "br"
}
-->
# Agentes de IA em Produção: Observabilidade e Avaliação

[![Agentes de IA em Produção](../../../translated_images/lesson-10-thumbnail.2b79a30773db093e0b4fb47aaa618069e0afb4745fad4836526cf51df87f9ac9.br.png)](https://youtu.be/l4TP6IyJxmQ?si=reGOyeqjxFevyDq9)

À medida que os agentes de IA passam de protótipos experimentais para aplicações no mundo real, torna-se essencial compreender seu comportamento, monitorar seu desempenho e avaliar sistematicamente seus resultados.

## Objetivos de Aprendizado

Após concluir esta lição, você saberá como/entenderá:
- Conceitos fundamentais de observabilidade e avaliação de agentes
- Técnicas para melhorar o desempenho, os custos e a eficácia dos agentes
- O que e como avaliar seus agentes de IA de forma sistemática
- Como controlar os custos ao implantar agentes de IA em produção
- Como instrumentar agentes construídos com AutoGen

O objetivo é equipá-lo com o conhecimento necessário para transformar seus agentes de "caixa preta" em sistemas transparentes, gerenciáveis e confiáveis.

_**Nota:** É importante implantar agentes de IA que sejam seguros e confiáveis. Confira também a lição [Construindo Agentes de IA Confiáveis](./06-building-trustworthy-agents/README.md)._

## Rastros e Etapas

Ferramentas de observabilidade como [Langfuse](https://langfuse.com/) ou [Azure AI Foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/what-is-azure-ai-foundry) geralmente representam as execuções de agentes como rastros e etapas.

- **Rastro** representa uma tarefa completa do agente do início ao fim (como lidar com uma consulta do usuário).
- **Etapas** são passos individuais dentro do rastro (como chamar um modelo de linguagem ou recuperar dados).

![Árvore de rastros no Langfuse](https://langfuse.com/images/cookbook/example-autogen-evaluation/trace-tree.png)

Sem observabilidade, um agente de IA pode parecer uma "caixa preta" – seu estado interno e raciocínio são opacos, dificultando o diagnóstico de problemas ou a otimização do desempenho. Com a observabilidade, os agentes tornam-se "caixas de vidro", oferecendo a transparência vital para construir confiança e garantir que operem conforme o esperado.

## Por que a Observabilidade é Importante em Ambientes de Produção

A transição de agentes de IA para ambientes de produção apresenta um novo conjunto de desafios e requisitos. A observabilidade deixa de ser um "luxo" e se torna uma capacidade essencial:

*   **Depuração e Análise de Causa Raiz**: Quando um agente falha ou gera um resultado inesperado, ferramentas de observabilidade fornecem os rastros necessários para identificar a origem do erro. Isso é especialmente importante em agentes complexos que podem envolver várias chamadas de LLM, interações com ferramentas e lógica condicional.
*   **Gestão de Latência e Custos**: Agentes de IA frequentemente dependem de LLMs e outras APIs externas que são cobradas por token ou por chamada. A observabilidade permite o rastreamento preciso dessas chamadas, ajudando a identificar operações excessivamente lentas ou caras. Isso possibilita que as equipes otimizem prompts, escolham modelos mais eficientes ou redesenhem fluxos de trabalho para gerenciar custos operacionais e garantir uma boa experiência do usuário.
*   **Confiança, Segurança e Conformidade**: Em muitas aplicações, é importante garantir que os agentes se comportem de maneira segura e ética. A observabilidade fornece um registro de auditoria das ações e decisões do agente. Isso pode ser usado para detectar e mitigar problemas como injeção de prompts, geração de conteúdo prejudicial ou tratamento inadequado de informações pessoalmente identificáveis (PII). Por exemplo, você pode revisar rastros para entender por que um agente forneceu uma determinada resposta ou utilizou uma ferramenta específica.
*   **Ciclos de Melhoria Contínua**: Dados de observabilidade são a base de um processo de desenvolvimento iterativo. Ao monitorar como os agentes se comportam no mundo real, as equipes podem identificar áreas para melhoria, coletar dados para ajuste fino de modelos e validar o impacto das mudanças. Isso cria um ciclo de feedback onde insights de produção a partir de avaliações online informam experimentos offline e refinamentos, levando a um desempenho progressivamente melhor dos agentes.

## Métricas-Chave para Monitorar

Para monitorar e compreender o comportamento dos agentes, uma variedade de métricas e sinais deve ser acompanhada. Embora as métricas específicas possam variar dependendo do propósito do agente, algumas são universalmente importantes.

Aqui estão algumas das métricas mais comuns monitoradas por ferramentas de observabilidade:

**Latência:** Com que rapidez o agente responde? Tempos de espera longos impactam negativamente a experiência do usuário. Você deve medir a latência para tarefas e etapas individuais rastreando as execuções do agente. Por exemplo, um agente que leva 20 segundos para todas as chamadas de modelo poderia ser acelerado usando um modelo mais rápido ou executando chamadas de modelo em paralelo.

**Custos:** Qual é o custo por execução do agente? Agentes de IA dependem de chamadas de LLM cobradas por token ou APIs externas. O uso frequente de ferramentas ou múltiplos prompts pode aumentar rapidamente os custos. Por exemplo, se um agente chama um LLM cinco vezes para uma melhoria marginal na qualidade, você deve avaliar se o custo é justificado ou se poderia reduzir o número de chamadas ou usar um modelo mais barato. O monitoramento em tempo real também pode ajudar a identificar picos inesperados (por exemplo, bugs causando loops excessivos de API).

**Erros de Requisição:** Quantas requisições o agente falhou? Isso pode incluir erros de API ou falhas em chamadas de ferramentas. Para tornar seu agente mais robusto contra esses problemas em produção, você pode configurar alternativas ou tentativas de repetição. Por exemplo, se o provedor de LLM A estiver fora do ar, você muda para o provedor de LLM B como backup.

**Feedback do Usuário:** Implementar avaliações diretas dos usuários fornece insights valiosos. Isso pode incluir classificações explícitas (👍positivo/👎negativo, ⭐1-5 estrelas) ou comentários textuais. Feedback negativo consistente deve alertá-lo, pois é um sinal de que o agente não está funcionando como esperado.

**Feedback Implícito do Usuário:** Comportamentos dos usuários fornecem feedback indireto mesmo sem classificações explícitas. Isso pode incluir reformulações imediatas de perguntas, consultas repetidas ou cliques no botão de tentar novamente. Por exemplo, se você perceber que os usuários repetidamente fazem a mesma pergunta, isso é um sinal de que o agente não está funcionando como esperado.

**Precisão:** Com que frequência o agente gera saídas corretas ou desejáveis? As definições de precisão variam (por exemplo, correção na resolução de problemas, precisão na recuperação de informações, satisfação do usuário). O primeiro passo é definir o que significa sucesso para o seu agente. Você pode rastrear a precisão por meio de verificações automatizadas, pontuações de avaliação ou rótulos de conclusão de tarefas. Por exemplo, marcar rastros como "bem-sucedidos" ou "falhos".

**Métricas de Avaliação Automatizada:** Você também pode configurar avaliações automatizadas. Por exemplo, pode usar um LLM para pontuar a saída do agente, avaliando se ela é útil, precisa ou não. Existem também várias bibliotecas de código aberto que ajudam a pontuar diferentes aspectos do agente. Por exemplo, [RAGAS](https://docs.ragas.io/) para agentes RAG ou [LLM Guard](https://llm-guard.com/) para detectar linguagem prejudicial ou injeção de prompts.

Na prática, uma combinação dessas métricas oferece a melhor cobertura da saúde de um agente de IA. No [notebook de exemplo](code_samples/10_autogen_evaluation.ipynb) deste capítulo, mostraremos como essas métricas aparecem em exemplos reais, mas primeiro aprenderemos como é um fluxo de trabalho típico de avaliação.

## Instrumente seu Agente

Para coletar dados de rastreamento, você precisará instrumentar seu código. O objetivo é instrumentar o código do agente para emitir rastros e métricas que possam ser capturados, processados e visualizados por uma plataforma de observabilidade.

**OpenTelemetry (OTel):** [OpenTelemetry](https://opentelemetry.io/) emergiu como um padrão da indústria para observabilidade de LLM. Ele fornece um conjunto de APIs, SDKs e ferramentas para gerar, coletar e exportar dados de telemetria.

Existem muitas bibliotecas de instrumentação que envolvem frameworks de agentes existentes e facilitam a exportação de spans do OpenTelemetry para uma ferramenta de observabilidade. Abaixo está um exemplo de como instrumentar um agente AutoGen com a biblioteca de instrumentação [OpenLit](https://github.com/openlit/openlit):

```python
import openlit

openlit.init(tracer = langfuse._otel_tracer, disable_batch = True)
```

O [notebook de exemplo](code_samples/10_autogen_evaluation.ipynb) deste capítulo demonstrará como instrumentar seu agente AutoGen.

**Criação Manual de Etapas:** Embora as bibliotecas de instrumentação forneçam uma boa base, muitas vezes há casos em que informações mais detalhadas ou personalizadas são necessárias. Você pode criar etapas manualmente para adicionar lógica personalizada de aplicação. Mais importante, elas podem enriquecer etapas criadas automaticamente ou manualmente com atributos personalizados (também conhecidos como tags ou metadados). Esses atributos podem incluir dados específicos do negócio, cálculos intermediários ou qualquer contexto que possa ser útil para depuração ou análise, como `user_id`, `session_id` ou `model_version`.

Exemplo de criação manual de rastros e etapas com o [Langfuse Python SDK](https://langfuse.com/docs/sdk/python/sdk-v3):

```python
from langfuse import get_client
 
langfuse = get_client()
 
span = langfuse.start_span(name="my-span")
 
span.end()
```

## Avaliação de Agentes

A observabilidade nos fornece métricas, mas a avaliação é o processo de analisar esses dados (e realizar testes) para determinar quão bem um agente de IA está funcionando e como ele pode ser melhorado. Em outras palavras, uma vez que você tenha esses rastros e métricas, como usá-los para julgar o agente e tomar decisões?

A avaliação regular é importante porque os agentes de IA frequentemente são não determinísticos e podem evoluir (através de atualizações ou mudanças no comportamento do modelo) – sem avaliação, você não saberia se seu "agente inteligente" está realmente cumprindo sua função ou se regrediu.

Existem duas categorias de avaliações para agentes de IA: **avaliação offline** e **avaliação online**. Ambas são valiosas e se complementam. Geralmente começamos com a avaliação offline, pois este é o passo mínimo necessário antes de implantar qualquer agente.

### Avaliação Offline

![Itens de conjunto de dados no Langfuse](https://langfuse.com/images/cookbook/example-autogen-evaluation/example-dataset.png)

Isso envolve avaliar o agente em um ambiente controlado, geralmente usando conjuntos de dados de teste, e não consultas ao vivo de usuários. Você utiliza conjuntos de dados curados onde sabe qual é o resultado esperado ou o comportamento correto e, em seguida, executa seu agente nesses dados.

Por exemplo, se você criou um agente para resolver problemas matemáticos, pode ter um [conjunto de dados de teste](https://huggingface.co/datasets/gsm8k) com 100 problemas e respostas conhecidas. A avaliação offline é frequentemente realizada durante o desenvolvimento (e pode fazer parte de pipelines de CI/CD) para verificar melhorias ou evitar regressões. O benefício é que ela é **repetível e você pode obter métricas claras de precisão, já que possui a verdade de base**. Você também pode simular consultas de usuários e medir as respostas do agente em relação às respostas ideais ou usar métricas automatizadas, como descrito acima.

O principal desafio da avaliação offline é garantir que seu conjunto de dados de teste seja abrangente e permaneça relevante – o agente pode ter um bom desempenho em um conjunto de teste fixo, mas encontrar consultas muito diferentes em produção. Portanto, você deve manter os conjuntos de teste atualizados com novos casos extremos e exemplos que reflitam cenários do mundo real. Uma combinação de pequenos casos de "teste rápido" e conjuntos de avaliação maiores é útil: conjuntos pequenos para verificações rápidas e maiores para métricas de desempenho mais amplas.

### Avaliação Online

![Visão geral de métricas de observabilidade](https://langfuse.com/images/cookbook/example-autogen-evaluation/dashboard.png)

Isso se refere à avaliação do agente em um ambiente ao vivo, no mundo real, ou seja, durante o uso real em produção. A avaliação online envolve monitorar o desempenho do agente em interações reais com usuários e analisar continuamente os resultados.

Por exemplo, você pode rastrear taxas de sucesso, pontuações de satisfação do usuário ou outras métricas no tráfego ao vivo. A vantagem da avaliação online é que ela **captura coisas que você pode não antecipar em um ambiente de laboratório** – você pode observar o desvio do modelo ao longo do tempo (se a eficácia do agente diminuir à medida que os padrões de entrada mudam) e identificar consultas ou situações inesperadas que não estavam em seus dados de teste. Ela fornece uma imagem real de como o agente se comporta no mundo real.

A avaliação online frequentemente envolve a coleta de feedback implícito e explícito dos usuários, como discutido, e possivelmente a execução de testes paralelos ou A/B (onde uma nova versão do agente é executada em paralelo para comparação com a antiga). O desafio é que pode ser difícil obter rótulos ou pontuações confiáveis para interações ao vivo – você pode depender de feedback do usuário ou métricas subsequentes (como se o usuário clicou no resultado).

### Combinando as duas

As avaliações online e offline não são mutuamente exclusivas; elas se complementam muito bem. Insights do monitoramento online (por exemplo, novos tipos de consultas de usuários onde o agente tem um desempenho ruim) podem ser usados para aumentar e melhorar os conjuntos de dados de teste offline. Por outro lado, agentes que têm um bom desempenho em testes offline podem ser implantados e monitorados online com mais confiança.

Na verdade, muitas equipes adotam um ciclo:

_avaliar offline -> implantar -> monitorar online -> coletar novos casos de falha -> adicionar ao conjunto de dados offline -> refinar o agente -> repetir_.

## Problemas Comuns

Ao implantar agentes de IA em produção, você pode encontrar vários desafios. Aqui estão alguns problemas comuns e suas possíveis soluções:

| **Problema**    | **Solução Potencial**   |
| ------------- | ------------------ |
| Agente de IA não executando tarefas de forma consistente | - Refine o prompt dado ao agente de IA; seja claro nos objetivos.<br>- Identifique onde dividir as tarefas em subtarefas e delegá-las a múltiplos agentes pode ajudar. |
| Agente de IA entrando em loops contínuos  | - Certifique-se de que há termos e condições claros de término para que o agente saiba quando parar o processo. |

## Resolução de Problemas Comuns

Aqui estão algumas estratégias para resolver problemas comuns ao trabalhar com agentes de IA:

| **Problema**                                | **Soluções**                                                                                                                                                                                                 |
|---------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| O agente não está respondendo corretamente  | - Ajuste os prompts para serem mais específicos.<br>- Forneça exemplos adicionais no prompt para ajudar o modelo a entender o contexto.<br>- Use um modelo maior para tarefas mais complexas.               |
| O agente não está lidando bem com tarefas complexas | - Para tarefas complexas que exigem raciocínio e planejamento, utilize um modelo maior especializado em tarefas de raciocínio.                                                                              |
| As chamadas de ferramentas do agente de IA não estão funcionando bem | - Teste e valide a saída da ferramenta fora do sistema do agente.<br>- Refine os parâmetros definidos, os prompts e os nomes das ferramentas.                                                              |
| O sistema multiagente não está funcionando de forma consistente | - Refine os prompts dados a cada agente para garantir que sejam específicos e distintos entre si.<br>- Construa um sistema hierárquico usando um agente "roteador" ou controlador para determinar qual agente é o mais adequado. |

Muitos desses problemas podem ser identificados de forma mais eficaz com a implementação de ferramentas de observabilidade. Os rastreamentos e métricas discutidos anteriormente ajudam a identificar exatamente onde, no fluxo de trabalho do agente, os problemas ocorrem, tornando o processo de depuração e otimização muito mais eficiente.

## Gerenciando Custos

Aqui estão algumas estratégias para gerenciar os custos de implantação de agentes de IA em produção:

**Usando Modelos Menores:** Modelos de Linguagem Pequenos (SLMs) podem ter um bom desempenho em certos casos de uso agentivos e reduzir significativamente os custos. Como mencionado anteriormente, construir um sistema de avaliação para determinar e comparar o desempenho em relação a modelos maiores é a melhor maneira de entender como um SLM se comportará no seu caso de uso. Considere usar SLMs para tarefas mais simples, como classificação de intenções ou extração de parâmetros, reservando modelos maiores para raciocínios mais complexos.

**Usando um Modelo Roteador:** Uma estratégia semelhante é usar uma diversidade de modelos e tamanhos. Você pode usar um LLM/SLM ou uma função serverless para direcionar solicitações com base na complexidade para os modelos mais adequados. Isso também ajudará a reduzir custos, garantindo desempenho nas tarefas certas. Por exemplo, direcione consultas simples para modelos menores e mais rápidos, e use modelos grandes e mais caros apenas para tarefas de raciocínio complexo.

**Cacheando Respostas:** Identificar solicitações e tarefas comuns e fornecer as respostas antes que elas passem pelo sistema agentivo é uma boa maneira de reduzir o volume de solicitações semelhantes. Você pode até implementar um fluxo para identificar o quão semelhante uma solicitação é às solicitações armazenadas em cache usando modelos de IA mais básicos. Essa estratégia pode reduzir significativamente os custos para perguntas frequentes ou fluxos de trabalho comuns.

## Vamos ver como isso funciona na prática

No [notebook de exemplo desta seção](code_samples/10_autogen_evaluation.ipynb), veremos exemplos de como podemos usar ferramentas de observabilidade para monitorar e avaliar nosso agente.

### Tem Mais Perguntas sobre Agentes de IA em Produção?

Participe do [Discord do Azure AI Foundry](https://aka.ms/ai-agents/discord) para se conectar com outros aprendizes, participar de horários de atendimento e tirar suas dúvidas sobre Agentes de IA.

## Aula Anterior

[Padrão de Design de Metacognição](../09-metacognition/README.md)

## Próxima Aula

[Protocolos Agentivos](../11-agentic-protocols/README.md)

---

**Aviso Legal**:  
Este documento foi traduzido utilizando o serviço de tradução por IA [Co-op Translator](https://github.com/Azure/co-op-translator). Embora nos esforcemos para garantir a precisão, esteja ciente de que traduções automáticas podem conter erros ou imprecisões. O documento original em seu idioma nativo deve ser considerado a fonte oficial. Para informações críticas, recomenda-se a tradução profissional realizada por humanos. Não nos responsabilizamos por quaisquer mal-entendidos ou interpretações equivocadas decorrentes do uso desta tradução.