<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "cdfd0acc8592c1af14f8637833450375",
  "translation_date": "2025-08-30T14:10:20+00:00",
  "source_file": "10-ai-agents-production/README.md",
  "language_code": "pt"
}
-->
# Agentes de IA em Produção: Observabilidade e Avaliação

[![Agentes de IA em Produção](../../../translated_images/lesson-10-thumbnail.2b79a30773db093e0b4fb47aaa618069e0afb4745fad4836526cf51df87f9ac9.pt.png)](https://youtu.be/l4TP6IyJxmQ?si=reGOyeqjxFevyDq9)

À medida que os agentes de IA passam de protótipos experimentais para aplicações no mundo real, torna-se essencial compreender o seu comportamento, monitorizar o seu desempenho e avaliar sistematicamente os seus resultados.

## Objetivos de Aprendizagem

Após concluir esta lição, saberá como/entenderá:
- Conceitos fundamentais de observabilidade e avaliação de agentes
- Técnicas para melhorar o desempenho, os custos e a eficácia dos agentes
- O que avaliar e como avaliar sistematicamente os seus agentes de IA
- Como controlar os custos ao implementar agentes de IA em produção
- Como instrumentar agentes construídos com AutoGen

O objetivo é equipá-lo com o conhecimento necessário para transformar os seus agentes de "caixa preta" em sistemas transparentes, geríveis e confiáveis.

_**Nota:** É importante implementar agentes de IA que sejam seguros e confiáveis. Consulte também a lição [Construir Agentes de IA Confiáveis](./06-building-trustworthy-agents/README.md)._

## Traços e Spans

Ferramentas de observabilidade como [Langfuse](https://langfuse.com/) ou [Azure AI Foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/what-is-azure-ai-foundry) geralmente representam as execuções de agentes como traços e spans.

- **Traço** representa uma tarefa completa do agente do início ao fim (como lidar com uma consulta de utilizador).
- **Spans** são passos individuais dentro do traço (como chamar um modelo de linguagem ou recuperar dados).

![Árvore de traços no Langfuse](https://langfuse.com/images/cookbook/example-autogen-evaluation/trace-tree.png)

Sem observabilidade, um agente de IA pode parecer uma "caixa preta" - o seu estado interno e raciocínio são opacos, dificultando o diagnóstico de problemas ou a otimização do desempenho. Com a observabilidade, os agentes tornam-se "caixas de vidro", oferecendo a transparência vital para construir confiança e garantir que operam conforme o esperado.

## Por que a Observabilidade é Importante em Ambientes de Produção

A transição de agentes de IA para ambientes de produção introduz um novo conjunto de desafios e requisitos. A observabilidade deixa de ser um "luxo" e torna-se uma capacidade crítica:

*   **Depuração e Análise de Causa Raiz**: Quando um agente falha ou produz um resultado inesperado, as ferramentas de observabilidade fornecem os traços necessários para identificar a origem do erro. Isto é especialmente importante em agentes complexos que podem envolver múltiplas chamadas a LLMs, interações com ferramentas e lógica condicional.
*   **Gestão de Latência e Custos**: Os agentes de IA frequentemente dependem de LLMs e outras APIs externas que são cobradas por token ou por chamada. A observabilidade permite o rastreamento preciso dessas chamadas, ajudando a identificar operações excessivamente lentas ou caras. Isto permite às equipas otimizar prompts, selecionar modelos mais eficientes ou redesenhar fluxos de trabalho para gerir custos operacionais e garantir uma boa experiência do utilizador.
*   **Confiança, Segurança e Conformidade**: Em muitas aplicações, é importante garantir que os agentes se comportam de forma segura e ética. A observabilidade fornece um registo de auditoria das ações e decisões do agente. Isto pode ser usado para detetar e mitigar problemas como injeção de prompts, geração de conteúdo prejudicial ou tratamento inadequado de informações pessoalmente identificáveis (PII). Por exemplo, pode rever traços para entender por que um agente forneceu uma determinada resposta ou utilizou uma ferramenta específica.
*   **Ciclos de Melhoria Contínua**: Os dados de observabilidade são a base de um processo de desenvolvimento iterativo. Ao monitorizar como os agentes se comportam no mundo real, as equipas podem identificar áreas de melhoria, recolher dados para ajustar modelos e validar o impacto das alterações. Isto cria um ciclo de feedback onde os insights de produção da avaliação online informam a experimentação offline e o refinamento, levando a um desempenho progressivamente melhor dos agentes.

## Métricas-Chave a Monitorizar

Para monitorizar e compreender o comportamento dos agentes, deve-se acompanhar uma série de métricas e sinais. Embora as métricas específicas possam variar consoante o propósito do agente, algumas são universalmente importantes.

Aqui estão algumas das métricas mais comuns monitorizadas por ferramentas de observabilidade:

**Latência:** Com que rapidez o agente responde? Tempos de espera longos impactam negativamente a experiência do utilizador. Deve medir a latência para tarefas e passos individuais, rastreando as execuções do agente. Por exemplo, um agente que demora 20 segundos para todas as chamadas ao modelo poderia ser acelerado utilizando um modelo mais rápido ou executando chamadas ao modelo em paralelo.

**Custos:** Qual é o custo por execução do agente? Os agentes de IA dependem de chamadas a LLMs cobradas por token ou APIs externas. O uso frequente de ferramentas ou múltiplos prompts pode aumentar rapidamente os custos. Por exemplo, se um agente chamar um LLM cinco vezes para uma melhoria marginal na qualidade, deve avaliar se o custo é justificado ou se poderia reduzir o número de chamadas ou usar um modelo mais barato. A monitorização em tempo real também pode ajudar a identificar picos inesperados (por exemplo, bugs que causam loops excessivos de API).

**Erros de Pedido:** Quantos pedidos o agente falhou? Isto pode incluir erros de API ou falhas em chamadas de ferramentas. Para tornar o seu agente mais robusto em produção, pode configurar alternativas ou tentativas de repetição. Por exemplo, se o fornecedor de LLM A estiver indisponível, pode alternar para o fornecedor de LLM B como backup.

**Feedback do Utilizador:** Implementar avaliações diretas dos utilizadores fornece insights valiosos. Isto pode incluir classificações explícitas (👍positivo/👎negativo, ⭐1-5 estrelas) ou comentários textuais. Feedback negativo consistente deve alertá-lo, pois é um sinal de que o agente não está a funcionar como esperado.

**Feedback Implícito do Utilizador:** Os comportamentos dos utilizadores fornecem feedback indireto, mesmo sem classificações explícitas. Isto pode incluir reformulações imediatas de perguntas, consultas repetidas ou cliques num botão de tentativa novamente. Por exemplo, se notar que os utilizadores fazem repetidamente a mesma pergunta, isto é um sinal de que o agente não está a funcionar como esperado.

**Precisão:** Com que frequência o agente produz resultados corretos ou desejáveis? As definições de precisão variam (por exemplo, correção na resolução de problemas, precisão na recuperação de informações, satisfação do utilizador). O primeiro passo é definir o que significa sucesso para o seu agente. Pode monitorizar a precisão através de verificações automáticas, pontuações de avaliação ou etiquetas de conclusão de tarefas. Por exemplo, marcar traços como "bem-sucedidos" ou "falhados".

**Métricas de Avaliação Automática:** Também pode configurar avaliações automáticas. Por exemplo, pode usar um LLM para pontuar a saída do agente, avaliando se é útil, precisa ou não. Existem também várias bibliotecas open source que ajudam a pontuar diferentes aspetos do agente. Por exemplo, [RAGAS](https://docs.ragas.io/) para agentes RAG ou [LLM Guard](https://llm-guard.com/) para detetar linguagem prejudicial ou injeção de prompts.

Na prática, uma combinação destas métricas oferece a melhor cobertura da saúde de um agente de IA. No [notebook de exemplo](code_samples/10_autogen_evaluation.ipynb) deste capítulo, mostraremos como estas métricas aparecem em exemplos reais, mas primeiro aprenderemos como é um fluxo de trabalho típico de avaliação.

## Instrumentar o seu Agente

Para recolher dados de traços, precisará de instrumentar o seu código. O objetivo é instrumentar o código do agente para emitir traços e métricas que possam ser capturados, processados e visualizados por uma plataforma de observabilidade.

**OpenTelemetry (OTel):** [OpenTelemetry](https://opentelemetry.io/) tornou-se um padrão da indústria para observabilidade de LLMs. Fornece um conjunto de APIs, SDKs e ferramentas para gerar, recolher e exportar dados de telemetria.

Existem muitas bibliotecas de instrumentação que envolvem frameworks de agentes existentes e facilitam a exportação de spans do OpenTelemetry para uma ferramenta de observabilidade. Abaixo está um exemplo de instrumentação de um agente AutoGen com a biblioteca de instrumentação [OpenLit](https://github.com/openlit/openlit):

```python
import openlit

openlit.init(tracer = langfuse._otel_tracer, disable_batch = True)
```

O [notebook de exemplo](code_samples/10_autogen_evaluation.ipynb) deste capítulo demonstrará como instrumentar o seu agente AutoGen.

**Criação Manual de Spans:** Embora as bibliotecas de instrumentação forneçam uma boa base, há casos em que informações mais detalhadas ou personalizadas são necessárias. Pode criar spans manualmente para adicionar lógica de aplicação personalizada. Mais importante, pode enriquecer spans criados automaticamente ou manualmente com atributos personalizados (também conhecidos como tags ou metadados). Estes atributos podem incluir dados específicos do negócio, cálculos intermédios ou qualquer contexto que possa ser útil para depuração ou análise, como `user_id`, `session_id` ou `model_version`.

Exemplo de criação manual de traços e spans com o [Langfuse Python SDK](https://langfuse.com/docs/sdk/python/sdk-v3):

```python
from langfuse import get_client
 
langfuse = get_client()
 
span = langfuse.start_span(name="my-span")
 
span.end()
```

## Avaliação de Agentes

A observabilidade fornece métricas, mas a avaliação é o processo de analisar esses dados (e realizar testes) para determinar quão bem um agente de IA está a desempenhar e como pode ser melhorado. Em outras palavras, uma vez que tenha esses traços e métricas, como os utiliza para julgar o agente e tomar decisões?

A avaliação regular é importante porque os agentes de IA são frequentemente não determinísticos e podem evoluir (através de atualizações ou alterações no comportamento do modelo) – sem avaliação, não saberia se o seu "agente inteligente" está realmente a fazer bem o seu trabalho ou se regrediu.

Existem duas categorias de avaliações para agentes de IA: **avaliação offline** e **avaliação online**. Ambas são valiosas e complementam-se. Normalmente, começamos com a avaliação offline, pois este é o passo mínimo necessário antes de implementar qualquer agente.

### Avaliação Offline

![Itens de dataset no Langfuse](https://langfuse.com/images/cookbook/example-autogen-evaluation/example-dataset.png)

Isto envolve avaliar o agente num ambiente controlado, tipicamente utilizando datasets de teste, e não consultas de utilizadores ao vivo. Utiliza datasets curados onde sabe qual é o resultado esperado ou o comportamento correto e, em seguida, executa o agente nesses dados.

Por exemplo, se construiu um agente para resolver problemas matemáticos, pode ter um [dataset de teste](https://huggingface.co/datasets/gsm8k) com 100 problemas e respostas conhecidas. A avaliação offline é frequentemente realizada durante o desenvolvimento (e pode fazer parte de pipelines CI/CD) para verificar melhorias ou prevenir regressões. A vantagem é que é **repetível e pode obter métricas de precisão claras, uma vez que tem uma verdade de base**. Também pode simular consultas de utilizadores e medir as respostas do agente em relação às respostas ideais ou usar métricas automáticas, como descrito acima.

O principal desafio da avaliação offline é garantir que o seu dataset de teste seja abrangente e permaneça relevante – o agente pode ter um bom desempenho num conjunto de testes fixo, mas encontrar consultas muito diferentes em produção. Portanto, deve manter os conjuntos de testes atualizados com novos casos extremos e exemplos que reflitam cenários do mundo real. Uma mistura de pequenos casos de "teste rápido" e conjuntos de avaliação maiores é útil: conjuntos pequenos para verificações rápidas e maiores para métricas de desempenho mais amplas.

### Avaliação Online

![Visão geral das métricas de observabilidade](https://langfuse.com/images/cookbook/example-autogen-evaluation/dashboard.png)

Isto refere-se a avaliar o agente num ambiente ao vivo, no mundo real, ou seja, durante a utilização real em produção. A avaliação online envolve monitorizar o desempenho do agente em interações reais com utilizadores e analisar continuamente os resultados.

Por exemplo, pode monitorizar taxas de sucesso, pontuações de satisfação do utilizador ou outras métricas em tráfego ao vivo. A vantagem da avaliação online é que **captura coisas que pode não antecipar num ambiente de laboratório** – pode observar a deriva do modelo ao longo do tempo (se a eficácia do agente diminuir à medida que os padrões de entrada mudam) e detetar consultas ou situações inesperadas que não estavam nos seus dados de teste. Fornece uma imagem real de como o agente se comporta no mundo real.

A avaliação online frequentemente envolve a recolha de feedback implícito e explícito dos utilizadores, como discutido, e possivelmente a realização de testes sombra ou testes A/B (onde uma nova versão do agente é executada em paralelo para comparação com a antiga). O desafio é que pode ser complicado obter etiquetas ou pontuações confiáveis para interações ao vivo – pode depender de feedback dos utilizadores ou métricas a jusante (como se o utilizador clicou no resultado).

### Combinando as duas

As avaliações online e offline não são mutuamente exclusivas; são altamente complementares. Insights da monitorização online (por exemplo, novos tipos de consultas de utilizadores onde o agente tem um desempenho fraco) podem ser usados para aumentar e melhorar os datasets de teste offline. Por outro lado, agentes que têm um bom desempenho em testes offline podem ser implementados e monitorizados online com mais confiança.

De facto, muitas equipas adotam um ciclo:

_avaliar offline -> implementar -> monitorizar online -> recolher novos casos de falha -> adicionar ao dataset offline -> refinar agente -> repetir_.

## Problemas Comuns

Ao implementar agentes de IA em produção, pode encontrar vários desafios. Aqui estão alguns problemas comuns e as suas potenciais soluções:

| **Problema**    | **Solução Potencial**   |
| ------------- | ------------------ |
| Agente de IA não realiza tarefas de forma consistente | - Refine o prompt dado ao agente de IA; seja claro nos objetivos.<br>- Identifique onde dividir as tarefas em subtarefas e delegá-las a múltiplos agentes pode ajudar. |
| Agente de IA entra em loops contínuos  | - Certifique-se de que tem termos e condições de terminação claros para que o agente saiba quando parar o processo. |

## Resolução de Problemas Comuns

Aqui estão algumas estratégias para resolver problemas comuns ao trabalhar com agentes de IA:

| **Problema**                                | **Soluções**                                                                                                                                                                                                 |
|---------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| O modelo não está a responder corretamente  | - Ajuste os prompts para serem mais claros e específicos.<br>- Experimente diferentes modelos para ver qual funciona melhor para o seu caso de uso.<br>- Ajuste os parâmetros do modelo para melhorar o desempenho. |
| O modelo não está a raciocinar corretamente | - Para tarefas complexas que exigem raciocínio e planeamento, utilize um modelo maior especializado em tarefas de raciocínio.                                                                               |
| As ferramentas chamadas pelo agente não estão a funcionar bem | - Teste e valide a saída da ferramenta fora do sistema do agente.<br>- Refine os parâmetros definidos, os prompts e os nomes das ferramentas.                                                              |
| O sistema multi-agente não está a funcionar de forma consistente | - Refine os prompts dados a cada agente para garantir que são específicos e distintos uns dos outros.<br>- Construa um sistema hierárquico utilizando um agente "roteador" ou controlador para determinar qual agente é o mais adequado. |

Muitos destes problemas podem ser identificados de forma mais eficaz com a implementação de ferramentas de observabilidade. As métricas e rastreamentos que discutimos anteriormente ajudam a identificar exatamente onde, no fluxo de trabalho do agente, os problemas ocorrem, tornando a depuração e a otimização muito mais eficientes.

## Gerir Custos

Aqui estão algumas estratégias para gerir os custos de implementar agentes de IA em produção:

**Utilizar Modelos Menores:** Modelos de Linguagem Pequenos (SLMs) podem ter um bom desempenho em certos casos de uso agentivos e reduzir significativamente os custos. Como mencionado anteriormente, construir um sistema de avaliação para determinar e comparar o desempenho em relação a modelos maiores é a melhor forma de entender como um SLM se comportará no seu caso de uso. Considere usar SLMs para tarefas mais simples, como classificação de intenções ou extração de parâmetros, reservando os modelos maiores para raciocínios mais complexos.

**Utilizar um Modelo Roteador:** Uma estratégia semelhante é usar uma diversidade de modelos e tamanhos. Pode utilizar um LLM/SLM ou uma função serverless para encaminhar pedidos com base na complexidade para os modelos mais adequados. Isto também ajudará a reduzir custos, garantindo ao mesmo tempo o desempenho nas tarefas certas. Por exemplo, encaminhe consultas simples para modelos menores e mais rápidos, e utilize apenas modelos grandes e dispendiosos para tarefas de raciocínio complexo.

**Cachear Respostas:** Identificar pedidos e tarefas comuns e fornecer as respostas antes que passem pelo seu sistema agentivo é uma boa forma de reduzir o volume de pedidos semelhantes. Pode até implementar um fluxo para identificar quão semelhante um pedido é em relação aos pedidos em cache, utilizando modelos de IA mais básicos. Esta estratégia pode reduzir significativamente os custos para perguntas frequentes ou fluxos de trabalho comuns.

## Vamos ver como isto funciona na prática

No [notebook de exemplo desta secção](code_samples/10_autogen_evaluation.ipynb), veremos exemplos de como podemos usar ferramentas de observabilidade para monitorizar e avaliar o nosso agente.

### Tem Mais Perguntas sobre Agentes de IA em Produção?

Junte-se ao [Discord do Azure AI Foundry](https://aka.ms/ai-agents/discord) para conhecer outros aprendizes, participar em horários de atendimento e obter respostas às suas perguntas sobre Agentes de IA.

## Aula Anterior

[Design Pattern de Metacognição](../09-metacognition/README.md)

## Próxima Aula

[Protocolos Agentivos](../11-agentic-protocols/README.md)

---

**Aviso Legal**:  
Este documento foi traduzido utilizando o serviço de tradução por IA [Co-op Translator](https://github.com/Azure/co-op-translator). Embora nos esforcemos para garantir a precisão, esteja ciente de que traduções automáticas podem conter erros ou imprecisões. O documento original no seu idioma nativo deve ser considerado a fonte oficial. Para informações críticas, recomenda-se uma tradução profissional realizada por humanos. Não nos responsabilizamos por quaisquer mal-entendidos ou interpretações incorretas resultantes do uso desta tradução.