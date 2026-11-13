+++
title = 'Recuperando Arquivos deletados no AWS S3 através da CLI'
date = 2021-12-18
draft = false
tags = ['AWS', 'S3', 'CLI']
languageCode = 'pt-br'
canonicalURL = 'https://blog.idopterlabs.com.br/recuperando-arquivos-deletados-no-aws-s3-através-da-cli-7c12fcaad430'
+++

Neste post iremos aprender como utilizar a [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) para recuperar objetos deletados em um bucket S3.

## Ativação de Versionamento de Objetos

A recuperação de objetos deletados é possível 🚨 **apenas em buckets com versionamento habilitado** 🚨. Por padrão, ao criarmos novos buckets, a funcionalidade de versionamento não é habilitada automaticamente.

Para verificar se um bucket S3 possui versionamento, podemos executar o comando a seguir:

```
aws s3api get-bucket-versioning --bucket guitar-store
```

Neste exemplo, o comando irá retornar o status de versionamento do bucket *guitar-store*. Um retorno vazio significa que o versionamento **não está habilitado.**

Para habilitar o versionamento do bucket, podemos executar o seguinte comando:

```
aws s3api put-bucket-versioning \
  --bucket guitar-store \
  --versioning-configuration Status=Enabled
```

Este comando não deve retornar nada. Ao rodarmos novamente o comando `get-bucket-versioning`, o seguinte resultado deve ser retornado:

```json
{
  "Status": "Enabled"
}
```

Este resultado indica que o versionamento está habilitado 🙌. A partir deste momento, caso sejam deletados, objetos do bucket podem ser recuperados.

## Listando *DeleteMarkers*

Quando um objeto é deletado de um bucket versionado, um **DeleteMarker** é criado para este objeto. O **DeleteMarker** é o que determina que um objeto foi deletado. O comando a seguir lista todos os objetos deletados do nosso bucket *guitar-store*:

```
aws s3api list-object-versions --bucket guitar-store \
  --query 'DeleteMarkers[?IsLatest==`true`]'
```

Caso nenhum objeto tenha sido deletado, o comando retorna o valor `null`.

É possível filtrar objetos de uma pasta específica com a opção `--prefix`, como no exemplo a seguir:

```
aws s3api list-object-versions --bucket guitar-store \
  --prefix 'guitar-specs' \
  --query 'DeleteMarkers[?IsLatest==`true`]'
```

Este comando retorna uma lista de **DeleteMarkers** referentes a objetos da pasta *guitar-specs*. A seguir, um exemplo de resultado incluindo um objeto deletado:

```json
[
  {
    "Owner": {
      "DisplayName": "...",
      "ID": "5abf415843b8b9696ed2a9fb5f283ef0759e3134"
    },
    "Key": "guitar-specs/guitar-4.pdf",
    "VersionId": "xq7BLXappOIJowZq9pgTPAe.Bric72yk",
    "IsLatest": true,
    "LastModified": "2021-12-14T16:30:12+00:00"
  }
]
```

Este resultado indica que o objeto *guitar-specs/guitar-4.pdf* foi deletado do bucket na data e hora indicada em `LastModified`.

## Deletando *DeleteMarkers*

A partir de informações retornadas na lista de **DeleteMarkers**, é possível recuperar cada um dos objetos deletados. Para cada objeto que desejamos recuperar, precisamos dos atributos `Key` e `VersionId`. No exemplo anterior, estes valores são:

- **guitar-specs/guitar-4.pdf**
- **xq7BLXappOIJowZq9pgTPAe.Bric72yk**

Com estes valores, executamos o comando a seguir:

```
aws s3api delete-object --bucket guitar-store \
  --key "guitar-specs/guitar-4.pdf" \
  --version-id "xq7BLXappOIJowZq9pgTPAe.Bric72yk"
```

Este comando deve retornar o resultado a seguir:

```json
{
  "DeleteMarker": true,
  "VersionId": "xq7BLXappOIJowZq9pgTPAe.Bric72yk"
}
```

Após executado o comando, o objeto deverá encontrar-se novamente no bucket e disponível para acesso — respeitando as mesmas permissões previamente estabelecidas.

## Conclusão

Neste post aprendemos como recuperar objetos deletados de buckets S3 através da AWS CLI. Para que isto seja possível, antes de mais nada é necessário que o bucket tenha a funcionalidade de versionamento habilitada. Por padrão, esta funcionalidade não é habilitada automaticamente para novos buckets, portanto é necessário (e recomendado!) que isto seja feito.

Além do S3, utilizamos vários outros serviços da AWS em nossos projetos na [IdopterLabs](https://www.idopterlabs.com.br/). Caso precise de ajuda em AWS, Elixir ou React Native para seu projeto, entre em contato conosco! 👉 **contato@idopterlabs.com.br**

---

*Publicado originalmente na [Idopter Labs](https://blog.idopterlabs.com.br/recuperando-arquivos-deletados-no-aws-s3-através-da-cli-7c12fcaad430).*
