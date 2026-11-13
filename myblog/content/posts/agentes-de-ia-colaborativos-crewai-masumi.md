+++
title = 'Agentes de IA Colaborativos: Um Case Study com CrewAI e Masumi Network'
date = 2025-05-26
draft = false
tags = ['AI Agent', 'Agentic AI', 'Blockchain', 'Cardano']
languageCode = 'pt-br'
canonicalURL = 'https://blog.idopterlabs.com.br/agentes-de-ia-colaborativos-um-case-study-com-crewai-e-masumi-network-f6c6370179d1'
+++

A [Idopter Labs](https://www.idopterlabs.com.br/) conquistou o terceiro lugar em uma *hackathon* de Agentes de Inteligência Artificial, desenvolvendo uma prova de conceito inovadora que conecta inteligência artificial à blockchain. O prêmio foi concedido pela [Masumi Network](https://www.masumi.network/), uma organização alemã que está criando uma rede financeira voltada para Agentes de IA.

## Colaboração entre Agentes

[Estudos recentes na área de IA](https://arxiv.org/abs/2307.07924) apontam que o maior avanço nos sistemas de agentes inteligentes não vem apenas da evolução dos grandes modelos de linguagem (LLMs como GPT, Gemini e *DeepSeek*), mas da capacidade de fazer agentes **trabalharem em equipe de forma colaborativa.** Quando colaboram, eles conseguem chegar a soluções mais rápido e com muito mais qualidade do que quando atuam sozinhos.

A solução desenvolvida durante a *hackathon* utilizou o [framework multi-agentes CrewAI](https://www.crewai.com/open-source). O CrewAI é um framework *open-source* em crescente adoção no mercado que permite a criação de equipes de Agentes de IA especializados e capazes de trabalhar juntos em tarefas complexas.

A rede da Masumi, atualmente em fase de testes, funciona como um marketplace descentralizado onde Agentes de IA podem contratar serviços uns dos outros de forma autônoma, com todas as decisões registradas na blockchain para que possam ser verificadas e auditadas, promovendo maior segurança e transparência.

## Entendendo a Solução

A solução desenvolvida pela Idopter Labs é totalmente *open-source* e está organizada em dois repositórios complementares no Github:

- [Masumi Cardano Agent](https://github.com/idopterlabs/masumi-cardano-agent) — O programa multi-agente que gera relatórios de tokens
- [Masumi Agent API](https://github.com/idopterlabs/masumi-cardano-agent-api) — Interface para integração com a rede Masumi

O programa multi-agente utiliza uma arquitetura baseada no CrewAI para analisar endereços da blockchain da Cardano e gerar relatórios detalhados sobre tokens. Para isso, foram desenvolvidas [ferramentas especializadas](https://docs.crewai.com/concepts/tools) que ampliam as capacidades dos agentes, permitindo-lhes consultar APIs externas e enriquecer o contexto disponível para os modelos de linguagem.

A solução integra dois serviços fundamentais do ecossistema Cardano:

- [**Kupo**](https://github.com/CardanoSolutions/kupo): Um indexador de blockchain que oferece consulta rápida e eficiente de saldos e UTXOs (Unspent Transaction Outputs)
- [**Token Registry**](https://github.com/cardano-foundation/cardano-token-registry): Um registro centralizado que fornece metadados detalhados sobre tokens nativos da Cardano

Combinando esses serviços, o sistema consegue identificar e reportar os tokens com maior volume em cada carteira analisada.

O segundo repositório é uma API web que expõe o serviço de relatório ao marketplace da Masumi seguindo o padrão [MIP-003](https://github.com/masumi-network/masumi-improvement-proposals/blob/main/MIPs/MIP-003/MIP-003.md) (Masumi Improvement Proposal 003), que define a especificação técnica para contratação automatizada de serviços entre agentes na rede.

Como você pode observar [na documentação da Masumi](https://docs.masumi.network/), o processo é não trivial. Ele envolve diferentes interações entre a API web e a rede Masumi, e também interações com *smart contracts.* Apesar da complexidade aparente, a experiência de desenvolvimento é significativamente simplificada graças à biblioteca de [SDK da Masumi](https://github.com/masumi-network/pip-masumi) e ao [Masumi Payment Service](https://github.com/masumi-network/masumi-payment-service), que abstraem as interações mais complexas com *smart contracts* e a rede blockchain.

## Observabilidade e Otimização de Custos

O monitoramento de custos é um aspecto crítico em projetos que utilizam APIs de LLMs pagas, onde cada chamada ao modelo representa um custo direto. Para gerenciar efetivamente esses gastos, implementamos observabilidade completa através do [Langtrace](https://www.langtrace.ai/), uma ferramenta de monitoramento suportada nativamente pelo CrewAI.

O Langtrace nos permite acompanhar em tempo real não apenas custos, mas também latência, taxa de sucesso das chamadas e padrões de uso dos tokens, fornecendo insights essenciais para otimização contínua.

Para este projeto, escolhemos a API da [OpenAI](https://openai.com/api/) devido à sua integração nativa com o CrewAI e à variedade de modelos disponíveis. A OpenAI oferece uma gama de opções — desde modelos otimizados para custo-benefício (como o GPT-4o-mini) até versões com capacidades avançadas de *reasoning* (como o GPT-4o), permitindo escolher a solução mais adequada para cada caso de uso. Para desenvolvimento, o modelo 4o-mini é suficiente e também o modelo padrão utilizado pelo CrewAI.

Para avaliar o custo-benefício de diferentes modelos, realizamos testes comparativos com cenários idênticos.

Resultados de uma execução do nosso programa utilizando o modelo **GPT 4o-mini**:

- **Tempo de processamento:** 117 segundos
- **Tokens consumidos:** ~30k
- **Custo total:** US$ 0,03

Resultados de uma nova execução utilizando o modelo **GPT 4o**:

- **Tempo de processamento:** 80 segundos (-46%)
- **Tokens consumidos:** ~28k
- **Custo total:** US$ 0,42 **(+1300%)**

A análise comparativa revela um *trade-off* interessante: embora o GPT-4o seja aproximadamente 46% mais rápido, seu custo é **14 vezes superior** ao GPT-4o-mini. Com base nesta análise de custo-benefício e considerando que nossa aplicação não exige velocidade extrema nem capacidades avançadas de *reasoning*, optamos por manter o GPT-4o-mini em produção. Esta decisão demonstra a importância de testar diferentes modelos e monitorar métricas de performance para otimizar custos sem comprometer a qualidade da solução.

> **💡 Dica**: Sempre inicie com modelos mais econômicos durante o desenvolvimento e teste modelos mais avançados apenas quando necessário. A economia pode ser significativa em projetos de larga escala!

## Conclusão

A participação da Idopter Labs na hackathon da Masumi Network demonstra o potencial transformador dos Agentes de IA colaborativos quando integrados com tecnologia blockchain. Nossa solução validou uma abordagem prática e eficiente para automatizar análises de dados *on-chain* usando o framework CrewAI.

Os resultados obtidos — desde a arquitetura multi-agente até a otimização de custos com modelos de LLM — ilustram que é possível desenvolver soluções robustas e economicamente viáveis para o mercado de agentes autônomos.

Mais do que um projeto de hackathon, esta solução representa um marco na jornada da Idopter Labs em direção à adoção mainstream de agentes de IA colaborativos, demonstrando que o futuro dos negócios automatizados já é uma realidade tangível.

**Está interessado em implementar agentes de IA colaborativos em sua empresa?** A Idopter Labs oferece consultoria especializada em desenvolvimento de soluções multi-agentes personalizadas para seus desafios específicos.

---

*Publicado originalmente na [Idopter Labs](https://blog.idopterlabs.com.br/agentes-de-ia-colaborativos-um-case-study-com-crewai-e-masumi-network-f6c6370179d1).*
