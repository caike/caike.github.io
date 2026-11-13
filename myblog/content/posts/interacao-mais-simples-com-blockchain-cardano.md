+++
title = 'A interação mais simples com a blockchain da Cardano via linha de comando'
date = 2022-02-07
draft = false
tags = ['Cardano', 'Blockchain', 'CLI']
languageCode = 'pt-br'
canonicalURL = 'https://caike.medium.com/a-interação-mais-simples-com-a-blockchain-da-cardano-via-linha-de-comando-b123abd02c62'
+++

Neste post aprenderemos uma das formas mais simples de interação com a blockchain da Cardano através da `cardano-cli` no Mac OS.

A comunicação com a blockchain da Cardano pela linha de comando é feita através de interações com um nó, ou "node".

Um nó é o elemento fundamental e mais importante da rede.

**A blockchain é uma rede de nós** que [comunicam-se entre si](https://en.wikipedia.org/wiki/Peer-to-peer) e executam diferentes papéis, como:

1- Execução do [protocolo](https://cardano.org/ouroboros/).

2- Validação e propagação de blocos.

3- Produção de blocos (apenas alguns dos nós fazem isso).

4- Execução de consultas na rede.

> **A blockchain é uma rede de nós que comunicam-se entre si e executam diferentes papéis.**

Uma vez que os nós possuem uma cópia da blockchain, eles também podem ser utilizados para executar consultas.

Existem algumas formas diferentes de rodar um nó. Aqueles com conhecimento de TI e administração de sistemas podem seguir o [processo manual](https://developers.cardano.org/docs/get-started/running-cardano/). Esta é certamente a forma mais recomendada caso o objetivo seja a construção de sistemas importantes, tais como o backend de uma [dApp](https://en.wikipedia.org/wiki/Decentralized_application) ou o gerenciamento de uma [Stake Pool](https://cardano.org/stake-pool-operation#stake-pool). Por outro lado, existe uma forma muito mais simples e "low-barrier" de rodar um nó da Cardano caso o objetivo seja estudar as operações básicas. Esta forma é através da utilização da carteira ("wallet") Daedalus.

A [Daedalus](https://daedaluswallet.io/), projeto da [Input Output](https://iohk.io/), a principal patrocinadora da Cardano, é uma das carteiras mais seguras e também uma das mais fáceis de instalar. Ela é uma carteira desktop que inclui seu próprio nó da blockchain da Cardano. Isso possibilita que seus usuários participem da rede, exercendo os papéis previamente mencionados.

Ao ser utilizada pela primeira vez, a Daedalus precisará baixar a blockchain inteira da Cardano desde [o primeiro bloco produzido](https://explorer.cardano.org/en/block?id=5f20df933584822601f9e3f8c024eb5eb252fe8cefb24d1317dc3d432e940ebb). Isso pode levar algumas horas ⏳

Uma fez concluída a sincronização do nó da Daedalus com a rede, a carteira estará pronta para utilização.

Este post não entrará em detalhes de como realizar operações com a carteira. Vamos pular esta parte e partir direto para a linha de comando 💪 Para isso, executaremos comandos no **Terminal.app**.

🚨 **Atenção:** os comandos a seguir funcionarão **apenas** quando a carteira estiver **completamente sincronizada.** 🚨

## Comunicando-se com a Testnet

Antes que seja possível a comunicação com o nó, é preciso configurar a variável de ambiente `CARDANO_NODE_SOCKET_PATH`. O valor desta variável será o caminho do socket utilizado pelo nó. Para a rede de testes, ou "Testnet", certifique-se da presença do arquivo `cardano-node.socket` no diretório a seguir:

`~/Library/Application\ Support/Daedalus\ Testnet/`

Uma vez confirmada a localização deste arquivo, execute o comando a seguir para popular a variável de ambiente:

```
export CARDANO_NODE_SOCKET_PATH=~/Library/Application\ Support/Daedalus\ Testnet/cardano-node.socket
```

Para confirmar que o valor foi corretamente atribuído, rode o comando a seguir e verifique que o resultado retornado é a localização do arquivo de socket:

```
echo $CARDANO_NODE_SOCKET_PATH
```

Agora vamos mudar para o diretório onde a Daedalus guarda a `cardano-cli` e outros executáveis da rede de testes. Rode o comando a seguir:

```
cd /Applications/Daedalus\ Testnet.app/Contents/MacOS/
```

Dentro deste diretório, verifique a presença da `cardano-cli`.

Este é o programa permitirá a interação com o nó. A partir desse diretório, execute o seguinte comando que consulta as informações mais recentes da blockchain:

```
./cardano-cli query tip --testnet-magic 1097911063
```

(O valor para `testnet-magic` é obtido através [deste arquivo](https://hydra.iohk.io/build/12179730/download/1/testnet-shelley-genesis.json). Este parâmetro é utilizado por funções criptográficas internas ao protocolo, e diferencia a comunicação com a rede de testes da comunicação com a rede principal.)

Um exemplo de resultado retornado é o JSON a seguir:

```json
{
  "era": "Alonzo",
  "epoch": 185,
  "hash": "886250a606c2d9968c5b1508e56cc2cd79f5abf99cb848695e8ecfb84c7b60dc",
  "block": 3299613,
  "slot": 49830257,
  "syncProgress": "100.00"
}
```

Caso o seu comando tenha retornado um resultado parecido com esse, parabéns! Você interagiu com a rede de testes da Cardano através da linha de comando 👏 Agora vamos aprender como fazer o mesmo na rede principal.

## Comunicando-se com a Mainnet

Os passos para comunicação com a rede principal, ou "Mainnet", são muito parecidos com aqueles para a comunicação com a rede de testes.

Certifique-se da presença do arquivo `cardano-node.socket` no diretório a seguir:

`~/Library/Application\ Support/Daedalus\ Mainnet/`

Execute o comando a seguir para popular a variável de ambiente:

```
export CARDANO_NODE_SOCKET_PATH=~/Library/Application\ Support/Daedalus\ Mainnet/cardano-node.socket
```

Rode o comando a seguir, e verifique que o resultado retornado é a localização do arquivo de socket da rede principal:

```
echo $CARDANO_NODE_SOCKET_PATH
```

Agora mude para o diretório aonde a Daedalus guarda a `cardano-cli` e outros executáveis da rede principal com o seguinte comando:

```
cd /Applications/Daedalus\ Mainnet.app/Contents/MacOS/
```

Dentro deste diretório, verifique a presença da `cardano-cli`.

A partir deste diretório, execute o seguinte comando para consultar as informações mais recentes da blockchain:

```
./cardano-cli query tip --mainnet
```

Note a utilização da opção `--mainnet` ao invés de `--testnet-magic`. Com esta opção, os comandos da `cardano-cli` são executados na rede principal, e não mais na rede de testes.

Um exemplo de resultado para esta consulta na rede principal é o JSON a seguir:

```json
{
  "era": "Alonzo",
  "epoch": 319,
  "hash": "869ed5d7788e5518ef7195c3731a2d37b9bd2e861820b377ef87f6f062edf640",
  "block": 6852938,
  "slot": 52675349,
  "syncProgress": "100.00"
}
```

Caso o seu comando tenha retornado um resultado parecido com esse, parabéns! Você interagiu com a rede principal da Cardano através da linha de comando 🤘🏻

O comando `query tip` é apenas um dos vários comandos disponíveis. Para aprender sobre os outros comandos, incluindo comandos para criar carteiras, realizar transações, operar stake pools, publicar smart contracts na rede e mais, consulte a [documentação de referência da cardano-cli](https://github.com/input-output-hk/cardano-node/blob/master/doc/reference/cardano-node-cli-reference.md).

## Conclusão

Uma forma muito simples e de rodar um nó e interagir com a blockchain da Cardano é através da utilização da Daedalus. A Daedalus é uma carteira desktop que inclui seu próprio nó da blockchain. Isso possibilita que seus usuários participem da rede e executem comandos através da `cardano-cli`.

Neste post aprendemos como rodar comandos na rede de testes, "Testnet", e rede principal, "Mainnet", utilizando a `cardano-cli`.

Gostou desse artigo e gostaria aprender mais sobre Cardano? Deixe seu comentário com sugestões ou então fale comigo [no twitter](https://twitter.com/caike)!

---

*Publicado originalmente no [Medium](https://caike.medium.com/a-interação-mais-simples-com-a-blockchain-da-cardano-via-linha-de-comando-b123abd02c62).*
