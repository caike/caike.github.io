+++
title = 'Prompts, Rules, Tools, MCP e Skills: Decifrando a Sopa de Letrinhas dos Coding Agents'
date = 2026-02-02
draft = false
tags = ['AI', 'Coding Agents', 'Claude Code', 'MCP']
languageCode = 'pt-br'
canonicalURL = 'https://blog.idopterlabs.com.br/prompts-rules-tools-mcp-e-skills-decifrando-a-sopa-de-letrinhas-dos-coding-agents-36e83d7f68cb'
+++

Se você está acompanhando a evolução dos agentes de código com IA, provavelmente já se deparou com uma sopa de letrinhas e conceitos que parecem ter sido inventados na semana passada: Prompts, Rules, Tools, MCP, Skills…

A boa notícia é que esses conceitos não são tão complicados quanto parecem. A má notícia? Quase ninguém explica direito a diferença entre eles. Então nesse post eu vou te ajudar com isso.

## Começando pelo Básico: Prompts

Prompts são as instruções em linguagem natural que você dá para o agente. É literalmente *o que* você quer que ele faça.

Se você já usou ChatGPT, Claude ou qualquer outro chat bot, você já escreveu prompts. Simples assim.

> "Analise esse código e me diga se tem algum problema de segurança: [código]"

Isso é um prompt. Não tem mágica. E aqui vai uma dica:

> O que torna um prompt eficaz é a sua **especificidade.**

Em vez de "revise meu código", você escreve "atue como um engenheiro de segurança sênior, analise esse código Elixir focando em vulnerabilidades de injeção em queries Ecto e exposição de dados sensíveis em logs, e formate a resposta como uma lista de issues com severidade."

**Quando usar prompts:** Sempre. É o ponto de partida de qualquer interação com agentes.

## Rules: O Comportamento no Piloto Automático

Agora as coisas começam a ficar mais interessantes. Rules são restrições persistentes que moldam como o agente se comporta ao longo do tempo e não apenas em uma única interação. É uma forma de memória do agente.

No **Claude Code** (nosso agente de escolha), por exemplo, temos algumas formas diferentes de definir rules. As duas mais importantes são:

**1. O arquivo `CLAUDE.md` na raiz do projeto** — perfeito para o essencial:

```
This is a web application written using the Phoenix web framework.

## Project guidelines

- Use `mix precommit` alias when you are done with all changes and fix
  any pending issues
- Use the already included and available `:req` (`Req`) library for HTTP
  requests, **avoid** `:httpoison`, `:tesla`, and `:httpc`. Req is included
  by default and is the preferred HTTP client for Phoenix apps
- ALWAYS write tests for new features
- NEVER create commits directly on the main branch
- ...
```

**2. O diretório `./claude/rules/`** — para rules mais específicas e organizadas:

```
.claude/
└── rules/
    ├── testing.md      # Convenções de testes
    ├── security.md     # Práticas de segurança
    └── database.md     # Padrões de acesso a dados
```

A sacada aqui é manter o `CLAUDE.md` enxuto com as regras fundamentais do projeto, enquanto rules mais detalhadas ou específicas de domínio ficam organizadas em arquivos separados.

A diferença crucial: **prompts** são instruções pontuais, **rules** (como já diz o nome) são regras que os prompts seguem durante a sua execução.

Para mais informações sobre rules e diferentes formas de defini-las, visite a seção de [Memory Management](https://code.claude.com/docs/en/memory) na documentação do Claude Code.

**Quando usar rules:** Quando você quer consistência no projeto sem ter que repetir as mesmas instruções em seus prompts.

## Tools: Dando Mãos para a IA

Até aqui, o agente só pensa e escreve de acordo com o conteúdo que foi utilizado [durante o treinamento dos seus respectivos modelos](https://en.wikipedia.org/wiki/Knowledge_cutoff). Tools são o que permitem ele *agir* no mundo real.

Uma tool é uma função que o agente pode invocar: ler arquivos, executar comandos no terminal, fazer requests HTTP, consultar bancos de dados.

No Claude Code, você tem [tools](https://code.claude.com/docs/en/settings#tools-available-to-claude) como:

- `Read` — ler arquivos e diretórios
- `Edit` — modificar código existente
- `Bash` — executar comandos no terminal
- `WebSearch` — buscar informações na internet

O fluxo é sempre o mesmo:

1. O agente decide que precisa usar uma tool
2. Chama a tool com os parâmetros necessários
3. Recebe o resultado
4. Usa esse resultado para continuar o raciocínio

É isso que transforma um chatbot em um *agente autônomo*. Sem tools, a IA é um cérebro sem corpo.

**Quando usar tools:** Quando o modelo precisa fazer algo além de gerar texto, tal como ler, escrever, executar, buscar.

## MCP: O USB dos Agentes de IA

Ok, tools são poderosas. Mas e se você precisar conectar seu agente a diferentes serviços externos? O prompt a seguir, por exemplo:

> Liste a issue mais recente desse projeto no Github e implemente uma solução

Será que é necessário implementar uma integração com o Github? É aí que entra o [MCP](https://code.claude.com/docs/en/mcp#connect-claude-code-to-tools-via-mcp).

Model Context Protocol (MCP) é um protocolo aberto que padroniza como agentes se conectam a sistemas externos. Ele é como a evolução natural das tools: em vez de definir cada função manualmente, você conecta a um **MCP Server** e ganha acesso a todas as tools que ele expõe podendo utilizá-las dinamicamente.

Quer conectar seu agente ao GitHub? [Tem MCP server](https://github.com/github/github-mcp-server) para isso. Figma? [Tem](https://help.figma.com/hc/en-us/articles/32132100833559-Guide-to-the-Figma-MCP-server). Seu banco Postgres? [Também](https://code.claude.com/docs/en/mcp#example:-query-your-postgresql-database). Algum sistema interno proprietário? Você pode criar um MCP server para ele.

```
Claude Code <--MCP-->   GitHub Server  <--> API do GitHub
                  |
                  +-->  Figma Server   <--> API do Figma
                  |
                  +--> Sistema Interno <--> Seus sistemas
```

É como se fosse um padrão USB para IA. Qualquer cliente MCP conecta em qualquer server MCP. Plug and play.

A grande sacada é que o agente descobre as tools disponíveis **em runtime.** Você não precisa recompilar, redeployar ou reconfigurar nada. Adicionou um novo MCP server? As tools dele já estão disponíveis ao agente.

**Quando usar MCP:** Quando você quer integrações reutilizáveis e padronizadas, especialmente se está construindo uma plataforma que outros vão estender.

## Skills: O Manual de Boas Práticas On-Demand

Se você acompanha as novidades do ecossistema de agente de IA, provavelmente viu esse conceito explodir nas últimas semanas. Skills viraram o assunto do momento.

Aqui está um conceito que muita gente confunde com prompts ou rules. [Skills](https://agentskills.io/home) são pacotes de conhecimento especializado que o agente consulta *sob demanda*, antes de executar uma tarefa específica.

Na prática, são arquivos markdown que contêm:

- Melhores práticas para uma tarefa específica
- Padrões de código que funcionam bem
- Armadilhas comuns a evitar
- Exemplos de uso

Você pede para o Claude Code escrever testes para alguma funcionalidade na sua aplicação Phoenix, por exemplo:

> Escreva testes para a funcionalidade de suspensão de usuários abusivos

Caso o agente tenha acesso a um arquivo `SKILL.md` dentro de uma pasta `elixir-phoenix-tests` e cujo [frontmatter](https://agentskills.io/specification#frontmatter-required) informe que a skill é relacionada a testes no Phoenix, o agente **automaticamente** trará o conteúdo do markdown para sua janela de contexto. Um exemplo de skills para testes em Phoenix é o seguinte:

```
When writing tests:
- Default tests to async: true
- NEVER call Application.put_env
```

(Caso queira saber por que **NUNCA** devemos chamar `Application.put_env` em nossos testes, eu explico nesse vídeo a seguir:)

Um grande benefício de utilizarmos skills é manter a nossa [janela de contexto](https://platform.claude.com/docs/en/build-with-claude/context-windows) enxuta. Em vez de carregar conteúdo muitas vezes desnecessário em toda interação, o agente busca apenas o que precisa, **quando precisa.**

A [Vercel](https://vercel.com/) lançou recentemente um portal de busca e consulta das skills mais utilizadas pela comunidade: [https://skills.sh/](https://skills.sh/)

**Quando usar Skills:** Para tarefas especializadas que requerem conhecimento específico de bibliotecas, formatos ou domínios.

## Juntando Tudo: Um Exemplo Real

Imagine que você pede para o agente: "Atualize o onboarding de novos usuários para puxar o endereço automaticamente a partir do CEP."

1. **Prompt:** A instrução que você acabou de dar. Clara, específica, com contexto suficiente para o agente entender o objetivo.
2. **Rules** (no `CLAUDE.md`): O agente varre as regras no `CLAUDE.md` e se depara com a seguinte regra: "Use a API dos Correios para consultas de CEP. Siga o padrão de contextos do projeto em `lib/my_app/contexts/`."
3. **Tools:** O agente usa `Read` para ler o código atual do onboarding, `Bash` para rodar os testes existentes, `Edit` para implementar a integração com a API de CEP, e `WebSearch` para consultar a documentação da API.
4. **Skills:** O agente lembra que precisa escrever testes, nota que existe uma skill relacionada a testes no Phoenix, e carrega essa skill antes de escrever os testes.
5. **MCP:** Conecta no MCP server do GitHub para criar um branch, abrir um PR com a implementação, e no server do Slack para avisar o time que a feature está pronta para review - e de repente até posta uma imagem engraçadinha também :-)

## Conclusão

A verdade é que daqui a alguns meses esse post provavelmente vai precisar de uma atualização. E tudo bem, faz parte do jogo.

O mais importante não é decorar cada conceito, mas **entender** que esses agentes possuem uma arquitetura por trás. Não é mágica. É engenharia.

Mas os fundamentos que vimos aqui com Prompts, Rules, Tools, MCP e Skill, formam a base de tudo que está sendo construído. Entender essas camadas hoje é o que vai separar quem usa IA de quem realmente tira proveito dela.

Se você ainda não experimentou essas técnicas com o Claude Code, comece simples: crie um `CLAUDE.md` no seu projeto e veja o que acontece. E para ler mais a fundo sobre esses conceitos e aprender como o Claude Code funciona, visite o site oficial [https://code.claude.com/docs/en/how-claude-code-works](https://code.claude.com/docs/en/how-claude-code-works)

E você, como está usando agentes de código no seu time? Já experimentou Claude Code ou outras ferramentas? Quais padrões funcionaram (ou falharam miseravelmente) para vocês?

Compartilha aí nos comentários!

---

*Publicado originalmente na [Idopter Labs](https://blog.idopterlabs.com.br/prompts-rules-tools-mcp-e-skills-decifrando-a-sopa-de-letrinhas-dos-coding-agents-36e83d7f68cb).*
