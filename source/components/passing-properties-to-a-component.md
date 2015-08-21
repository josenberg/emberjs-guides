Por padrão o componente não tem acesso às propriedades que são do escopo do template onde são usadas.

Por exemplo, imagine que você tem um componente chamado `blog-post` que é usado para 
mostrar um post em um blog:

```app/templates/components/blog-post.hbs
<h1>Component: {{title}}</h1>
<p>Lorem ipsum dolor sit amet.</p>
```

Você pode ver que existe uma expressão Handlebars `{{title}}` para 
imprimir na tela o valor da propriedade `title` dentro do elemento `<h1>`.

Agora imagine que nos temos o seguinte template e rota:

```app/routes/index.js
export default Ember.Route.extend({
  model() {
    return {
      title: "Rails is omakase"
    };
  }
});
```

```app/templates/index.hbs

<h1>Template: {{title}}</h1>
{{blog-post}}
```

Rodando esse codigo, você vai que o primeiro `<h1>` (do template externo)
mostra a propriedade `title`, mas o segundo `<h1>` (de dentro do componente)
é vazio.

Nos podemos corrigir isso fazendo que a propriedade `title` seja disponivel para o componente

```handlebars
{{blog-post title=title}}
```

Isso fará a propriedade `title` do escopo externo tambem disponivel para ser usada
dentro do template do componente, com o mesmo nome, `title`.

Se, no exemplo abaixo, a propriedade `title` do modelo fosse chamada de `name`, você declararia o componente assim:

```handlebars
{{blog-post title=name}}
```

Em outras palavras, quando você esta fazendo bind de uma propriedade do escopo externo
para uma variavel dentro do escopo do componente, você usa a syntax `componentProperty=outerProperty`.

Isso é importante para notar que o valor dessas propriedades esta ligado.
Sempre que você mudar o valor dela de dentro ou de fora do seu componente 
os valores serão sincronizados. No exempl a seguir, escreva algum texto 
na caixa de entrada tanto externa, quanto interna do nosso comoponente
e veja como eles se comportam de maneira sincronizada:

Você tambem pode fazer bind de propriedades de dentro de um `{{#each}}` loop. 
Isso irá criar um componente para cada item e fazer bind dele para cada modelo no loop.

```handlebars
{{#each model as |post|}}
  {{blog-post title=post.title}}
{{/each}}
```
Se você esta usando o helper `{{component}}` para renderizar seu componente, você pode passar propriedades
para o componente escolhido da mesma maneira:

```handlebars
{{component componentName title=title name=name}}
```

### Positional Params

Alem de passar atributos para um componente, você tambem pode passar parametros posicionais, 
como nos vimos com o `{{link-to}}`, ex. `{{link-to "user" userModel}}`.

Você pode acessar esses parametros configurando o atributo `positionalParams` na classe do seu componente.

```app/components/x-visit.js
const MyComponent = Ember.Component.extend();

MyComponent.reopenClass({
>>>>>>> Documenting usage of positional params in reopenClass
  positionalParams: ['name', 'model']
});

export default MyComponent;
```

Isso vai expor o primeiro parametro no seu template como um atributo chamado `{{name}}` e o segundo como `{{model}}`. 
Eles tambem vão ficar disponiveis como atributos reculares no seu componente via `this.get('name')` and `this.get('model')`.

Alem de mapear os parametros para atributos, você pode tambem ter um numero arbitrario de parametros configurando `positionalParams` como uma string, 
ex. `positionalParams: 'params'`. isso tambem vai permitir acesso à esses parametros em um array, como no exemplo abaixo:

```app/templates/components/x-visit.hbs
{{#each params as |param|}}
  {{param}}
{{/each}}
```