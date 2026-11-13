+++
title = 'Selects dinâmicos com LiveView'
date = 2022-03-24
draft = false
tags = ['Elixir', 'Phoenix', 'LiveView']
languageCode = 'pt-br'
canonicalURL = 'https://blog.idopterlabs.com.br/selects-dinâmicos-com-liveview-b2c6d48a40ba'
+++

A utilização de selects dinâmicos é uma excelente forma de filtrar uma listagem muito grande e tornar a navegação de uma aplicação mais amigável.

Neste post iremos construir selects dinâmicos com *Phoenix* e *LiveView*. A aplicação de exemplo é uma simples página de listagem de guitarras.

(O segundo select é preenchido dinamicamente, de acordo com o valor do primeiro)

Uma guitarra pertence a uma marca, como Gibson ou Fender. Cada marca oferece algumas opções diferentes de modelo, como Les Paul e SG no caso da Gibson, ou Stratocaster e Telecaster no caso da Fender.

Diante da página de listagem de guitarras, os usuários podem selecionar a marca no primeiro select. De acordo com a marca selecionada, os modelos pertencentes a marca são populados no segundo select.

## Renderizando a página

O primeiro passo é a criação da rota que associa uma URL a uma *LiveView*. Neste exemplo, associamos a URL raíz `/` ao módulo `GuitarLive.Index` através da seguinte definição no arquivo *router.ex*:

```elixir
scope "/", InstrumentStoreSelectWeb do
  pipe_through(:browser)

  live("/", GuitarLive.Index, :index)
end
```

O módulo `GuitarLive.Index` é responsável pelas definições das *callback functions* executadas de acordo com o ciclo de vida da *LiveView* e interações dos usuários com a página. As callback functions são `mount/3` e `handle_event/3`.

A função `mount/3` é responsável pelos dados iniciais que serão exibidos na página.

```elixir
def mount(_params, _session, socket) do
  brands = Inventory.list_unique_brands()

  {:ok,
   socket
   |> assign(:guitars, [])
   |> assign(:models, [])
   |> assign(:selected_brand, nil)
   |> assign(:brands, brands)}
end
```

Nessa função, fazemos uma chamada à função `list_unique_brands/0`, parte do módulo `Inventory`. Esse módulo é o context module, responsável por encapsular os detalhes de acesso ao banco de dados. A função `list_unique_brands/0` retorna uma lista de marcas que será usada para popular o primeiro select no template *index.html.heex* a seguir:

```html
<form phx-change="set-filter">
  <select name="brand" id="brand">
    <option/>
    <%= for brand <- @brands do %>
      <option value={brand}
        selected={if @selected_brand == brand, do: "selected"}>
        <%= brand %>
      </option>
    <% end %>
  </select>
</form>
```

## Respondendo a eventos do DOM

Uma das formas mais simples de responder a eventos do DOM em *LiveView* é através de bindings. O binding `phx-change` é responsável por "ligar" qualquer evento de mudança no formulário a função `handle_event/3`, definida no módulo `GuitarLive.Index`.

De volta a `GuitarLive.Index`, implementamos a primeira versão dessa função:

```elixir
def handle_event("set-filter",
  %{"_target" => ["brand"], "brand" => brand}, socket) do

  {:noreply,
   socket
   |> assign(:models, Inventory.filter_models_by_brand(brand))
   |> assign(:selected_brand, brand)}
end
```

Utilizamos *pattern matching* para interceptar o evento (`"set-filter"`), os dados da origem do evento (`"_target" => ["brand"]`), valor selecionado no primeiro select (`"brand" => brand`), e o socket referente ao usuário interagindo com a página (`socket`).

Uma vez selecionada a marca, a função `handle_event/3` irá filtrar os modelos disponíveis desta marca através da função `filter_models_by_brand/1` do módulo `Inventory`. Os modelos de guitarra estarão disponíveis no `socket` através da propriedade `models`, que pode ser acessada no template através de `@models`:

```html
<select name="model" id="model">
  <option/>
  <%= for model <- @models do %>
    <option value={model}><%= model %></option>
  <% end %>
</select>
```

No que diz respeito a selects dinâmicos, a feature está concluída! Ao detectar mudanças no primeiro select, a função `handle_event/3` será executada pelo *Phoenix* e irá popular o socket com os dados a serem utilizados pelo segundo select 💥

## Listando as guitarras

A última etapa é a listagem das guitarras, filtradas por marca e modelo. Para isso, definimos uma nova *clause* da função `handle_event/3` conforme o código a seguir:

```elixir
def handle_event("set-filter",
  %{"_target" => ["model"], "model" => model}, socket) do

  selected_brand = socket.assigns.selected_brand
  guitars = Inventory.filter_by_brand_and_model(selected_brand, model)

  {:noreply, assign(socket, :guitars, guitars)}
end
```

A assinatura dessa função é bem parecida com a anterior, mas não é exatamente igual. Note a diferença no segundo argumento:

`%{"_target" => ["brand"], "brand" => brand}`

vs.

`%{"_target" => ["model"], "model" => model}`

Ambos os elementos `<select>` emitem o mesmo evento `"set-filter"`, porém, o primeiro emite valores para `brand` enquanto o segundo emite valores para `model`.

Uma vez populada com os valores das guitarras a serem exibidas, a socket permite que o template leia esses valores e monte a tabela:

```html
<tbody id="guitars">
  <%= for guitar <- @guitars do %>
    <tr id={"guitar-#{guitar.id}"}>
      <td><%= guitar.brand %></td>
      <td><%= guitar.model %></td>
      <td><%= guitar.year %></td>
    </tr>
  <% end %>
</tbody>
```

A listagem está concluída!

## Conclusão

A utilização de selects dinâmicos é uma excelente forma de filtrar uma listagem muito grande e tornar a navegação mais amigável. Aprendemos como implementar uma forma de select dinâmico com *Phoenix* e *LiveView*, através de *bindings* e *callback functions*.

Para os interessados em explorar o código fonte da aplicação de exemplo mais a fundo, ele está disponível no link a seguir:

[GitHub - idopterlabs/InstrumentStoreSelect](https://github.com/idopterlabs/InstrumentStoreSelect)

Espero que este post ajude você em seu próximo projeto. Caso sua empresa precise de ajuda na construção de aplicações *Elixir* e *LiveView*, [entre em contato!](https://www.idopterlabs.com.br/)

---

*Publicado originalmente na [Idopter Labs](https://blog.idopterlabs.com.br/selects-dinâmicos-com-liveview-b2c6d48a40ba).*
