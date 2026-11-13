+++
title = 'ElixirConf US 2023'
date = 2023-09-20
draft = false
tags = ['Elixir', 'Phoenix', 'LiveView', 'Conferences']
languageCode = 'pt-br'
canonicalURL = 'https://blog.idopterlabs.com.br/elixirconf-us-2023-f41d11adf0e4'
+++

Em setembro, Orlando voltou a ser palco da ElixirConf US após 7 anos desde sua última edição na cidade. Tive o privilégio de participar novamente desse encontro de três dias, repleto de palestras sobre variados temas relacionados ao ecossistema Elixir e BEAM, organizadas em três trilhas simultâneas. A qualidade das palestras foi tão alta que se tornou um desafio decidir qual assistir! A seguir, faço um resumo das que tive a oportunidade de acompanhar.

## Dia 1

O evento começou com a Keynote do Chris McCord, criador do Phoenix e LiveView. Em sua palestra, Chris apresentou o progresso de ambos os projetos e algumas das novidades que estão por vir. O vídeo de sua apresentação encontra-se disponível no canal da ElixirConf no Youtube, mas gostaria de mencionar uma das features que mais me chamou atenção: uma nova API para gerenciamento de tarefas assíncronas em LiveView. Essa nova API é uma abstração em cima de padrões para tarefas assíncronas em LiveView, e ajuda principalmente no tratamento de "edge-cases" que costumam ser deixados de lado ou implementados de forma incorreta.

Após a Keynote, assisti a palestra da Elaine Watanabe. Já havia assistido algumas palestras virtuais da Elaine e também escutado sua entrevista no podcast Elixir em Foco, então foi um prazer poder finalmente conhecê-la pessoalmente. Em sua palestra, Elaine falou sobre "code smells" em Elixir e apresentou diversas soluções para tornar nosso código Elixir mais facilmente compreensível e manutenível.

Em seguida veio a palestra de Charlie Holtz sobre como utilizar Elixir para criar aplicações que utilizam Inteligência Artificial. Charlie mostrou como padrões de aplicações IA conseguem ser mapeados para padrões comumente utilizados em Elixir e OTP.

Utilizando a API do <https://replicate.com/>, Charlie demonstrou um exemplo de rede social onde cada um dos participantes é um agente AI com comportamento próprio e único. Um dos agentes criados durante sua palestra foi o "José Valim" 😆 No geral, uma excelente apresentação com muita descontração!

Em "State: A necessary Evil", Isaac Yonemoto apresentou 11 formas diferentes de trabalharmos com estado persistente em Elixir. Para cada uma das formas, Isaac apresentou benefícios e malefícios, seguido de exemplos práticos e de simples compreensão. Descobri durante sua palestra que Isaac é o criador do projeto Zigler, que permite utilizar código Zig diretamente de aplicações Elixir através de Native Implemented Functions (NIFs).

A palestra seguinte foi a de Savannah Manning. Ela contou sua história de mudança de carreira de turismo em escalada para o mercado de desenvolvimento de software, e deu algumas dicas de como desenvolvedores iniciantes podem evoluir em suas carreiras (livros, sites, práticas, etc.).

AJ Foster foi o palestrante seguinte, e falou sobre alguns obstáculos enfrentados com as atuais soluções de geração de código para APIs. AJ apresentou seu projeto Open API Generator que almeja solucionar alguns desses obstáculos.

Em seguida, Andrew Selder apresentou estratégias para compilação de templates dinâmicos em aplicações LiveView. As técnicas apresentadas por Andrew envolvem aplicação (cautelosa) de metaprogramação em Elixir utilizadas em projetos da Estee Lauder.

A palestra final do dia 1 foi de Brian Cardarella, CEO da DockYard. Brian apresentou o progresso do LiveView native, uma solução para desenvolvimento de aplicações móveis em Elixir em LiveView. Apesar de React Native e Flutter servir-nos muito bem para nossos apps na Idopter Labs, estamos sempre abertos a novas formas de melhorar nosso trabalho e por isso vamos manter o LiveView Native no radar.

## Dia 2

A primeira palestra do segundo dia foi de Katelynn Burns, que apresentou uma aplicação de "motion tracking" utilizando Bumblebee e LiveView. Bumblebee é uma biblioteca Elixir construída em cima do Nx, que permite trabalharmos com modelos existentes de redes neurais de forma extremamente simples. Kate mostrou em sua palestra como podemos adaptar modelos do HuggingFace para casos de uso específicos.

A palestra seguinte foi de Jenny Bramble, que deu uma verdadeira aula de técnicas de testes que podem ser utilizadas por times de desenvolvimento para aumentar a cobertura e eficiência de testes Black Box.

Uma palestra bem técnica mas muito interessante foi a de Rafał Studnicki, que falou sobre como a sua empresa Whatnot faz deployment de serviços "stateful" em Elixir. Apesar de Elixir e Phoenix facilitarem bastante o desenvolvimento de aplicações "real-time", a parte de deployment ainda impõe diversos desafios. Rafał apresentou estratégias de rolling deployments preservando conexões existentes e também dicas de como escrever testes automatizados que verificam o comportamento da aplicação em um ambiente clusterizado.

A talk seguinte foi sobre Ash, um framework Elixir que vem começando a ganhar tração na comunidade. Alguns já haviam me falado do Ash, mas ainda não tive a oportunidade de experimentá-lo. A palestra de Zach Daniels, o criador do Ash, apresentou o progresso do trabalho feito até o momento, e as features que estão por vir. O Ash tem como objetivo facilitar a construção de aplicações centradas no domínio (domain). Seu slogan é "Model your domain, derive the rest". Os trechos de código no site oficial me lembraram bibliotecas Ruby como Grape e Sinatra. Tenho a impressão de que o Ash permite fazer muita coisa com relativamente pouco código escrito, e estou ansioso para testar a sua proposta em alguns dos meus projetos pessoais. A minha primeira experiência no site deles já me deixou com uma boa impressão — tiveram a sacada de criar um tutorial interativo utilizando LiveBook!

Andrew Bennett do Whatsapp mostrou algumas ciladas comuns em um ambiente clusterizado, e que podem causar falhas irrecuperáveis no sistema. Em se tratando de um serviço como o Whatsapp, com ~2 bilhões de usuários ativos e clusters com ~30,000 nodes, tais falhas podem causar uma catástrofe! Andrew apresentou o projeto erldist_filter que permite logar e filtrar mensagens trocadas em um ambiente BEAM clusterizado, e como isso vem ajudando o Whatsapp a se proteger de acidentes e evitar downtime.

Em seguida assisti a palestra do German Velasco. É um prazer aprender com o German! Venho acompanhando seu conteúdo faz algum tempo, e tive a oportunidade de finalmente conhecê-lo pessoalmente antes de sua palestra. German falou sobre o polêmico assunto de "Contexts", e como aplicar conceitos de Domain Driven Design para melhorar nosso código.

Ao final do segundo dia tivemos as lighting talks ⚡️ Uma série de apresentações curtas porém não menos interessantes. Não lembro de todas elas, porém gostaria de mencionar a do Paulo Valente, mantenedor do Nx. Paulo mostrou como foi construído o ElixirConf Chess utilizando LiveView Native e Nx.

## Dia 3

Uma das minhas palestras favoritas do terceiro dia foi a do Chris Gregori. Ele apresentou diferentes maneiras de trabalhar com tarefas assíncronas em LiveView. Sua talk mencionou algumas das estratégias previamente apresentadas pelo Chris McCord em sua keynote e foi além, incluindo também técnicas que levam em consideração a experiência dos usuários (UX).

José Valim fechou o evento com uma keynote falando sobre o sistema de tipagem estática que está sendo incorporado em Elixir. Este trabalho é o resultado de um estudo feito por ele, Giuseppe Castagna e Guillaume Duboc, e que está passando da fase de pesquisa para a fase de implementação. José explicou porque o sistema de tipos para Elixir não pode simplesmente ser trazido de outras linguagens devido a natureza única da BEAM.

Um momento delicado de sua keynote foi quando José mencionou o risco de que a adição de tipagem estática na linguagem cause uma divisão na comunidade, entre aqueles que adotam e aqueles que rejeitam.

Como fã de linguagens estaticamente tipadas como Golang, Rust e Haskell, estou muito empolgado com a possibilidade de adotar mais uma ferramenta que pode auxiliar na modelagem do domínio e de regras de negócio, contando com mais verificações em tempo de compilação.

## Conclusão

Participar da ElixirConf US 2023 foi uma experiência extremamente enriquecedora. Mais do que as palestras, foi uma chance de interação e conexão com a comunidade — o chamado "hallway track".

Posso dizer que saí da conferência muito empolgado com o futuro da tecnologia e ainda mais inspirado em continuar construindo soluções incríveis em Elixir na Idopter Labs, e que apoiem e impactem positivamente empresas, empreendedores e seus clientes.

---

*Publicado originalmente na [Idopter Labs](https://blog.idopterlabs.com.br/elixirconf-us-2023-f41d11adf0e4).*
