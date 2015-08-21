Para definir um componente, crie um template no diretorio `components/`,
o nome dele de ver com hifen, `{{blog-post}}`, por exemplo, então o template do
componente `{{blog-post}}`, seria `components/blog-post`.


**Nota:** Componentes devem ter pelo menu um hifen em seu nome. 
Então `blog-post` é um nome aceitavel, assim como `audio-player-controls`, 
porem `post` não é. Isso prevente conflitos que podem acontecer com futuros elementos HTML
e tambem garantir que o Ember detecte os componentes de forma automatica.

Um exemplo de template seria algo como:

```bash
ember generate component blog-post
```

```app/templates/components/blog-post.hbs
<h1>Blog Post</h1>
<p>Lorem ipsum dolor sit amet.</p>
```

Ter um template na pasta `components/` cria um componente do mesmo nome. 
dado o template acima você agora pode usar um template costumizado do `{{blog-post}}`:

```app/templates/index.hbs
{{#each model as |post|}}
  {{#blog-post title=post.title}}
    {{post.body}}
  {{/blog-post}}
{{/each}}
```

```app/templates/components/blog-post.hbs
<article class="blog-post">
  <h1>{{title}}</h1>
  <p>{{yield}}</p>
  <p>Edit title: {{input type="text" value=title}}</p>
</article>
```

```app/routes/index.js
var posts = [{
    title: "Rails is omakase",
    body: "There are lots of à la carte software environments in this world."
  }, {
    title: "Broken Promises",
    body: "James Coglan wrote a lengthy article about Promises in node.js."
}];

export default Ember.Route.extend({
  model() {
    return posts;
  }
});
```

```app/components/blog-post.js
export default Ember.Component.extend({
});
```

Cada componente, por baixo, é cercado por um elemento. Por padrão 
o Ember irá usar uma `<div>` para conter o template do seu componente.
Para aprender à mudar o elemento que o Ember usa para seu componente veja
[Customizing a Component's
Element](../customizing-a-components-element).


### Definindo a Subclasse de um Componente

Algumas vezes, seu componente irá encapsular alguns codi
Often times, your components will just encapsulate certain trechos de Handlebars
que você vai acabar usando diversas vezes. Nesses casos você não precisa escrever nenhum 
JavaScript. Apenas defina esse template Handlebar como se ele fosse o template que você 
escolher.

Se você precisar costumizar o comportamento de um componente você irá 
precisar definir uma subclasse do `Ember.Component`. Por exemplo, você
iria precisar de uma custom subclass se você quisesse mudar o elemento do componente,
reponder à actions de template do componente ou manualmente mudar esse elemento
utilizando JavaScript.

Ember sabe qual subclasse pertence à cada componente baseado no nome do arquivo. 
Por exemplo, se você tiver um componente chamado `blog-post`, você criaria um
arquivo chamado `app/components/blog-post.js`. Se seu componente fosse chamado
`audio-player-controls`, seu arquivo estaria em `app/components/audio-player-controls.js`.

### Renderizando Dinamicamente um componente

O helper `{{component}}` pode ser usado para renderizar um componente durante o tempo de execução. 
A syntax do `{{my-component}}` irá sempre renderizar o mesmo componente,
No entado usando o helper `{{component}}` permite você trocar de renderizado durante a execução. 
Isso é bastante em casado onde, por exemplo, você quer interagir com diferentes bibliotecas externas.
Usando helper `{{component}}` você poderia mantes duas logicas diferente bem separadas.

O primeiro parametro do helper é o nome do componente para renderizar, como uma string. Então se tivermos
`{{component 'blog-post'}}` é a mesma coisa que `{{blog-post}}`.

O valor real de `{{component}}` vem dele conseguir escolher 
dinamicamente qual componente renderizar. Abaixo esta um exemplo de uso do helper 
sendo usado para renderizar diferentes tipos de posts:

```app/templates/components/foo-component.hbs
<h3>Hello from foo!</h3>
<p>{{post.body}}</p>
```

```app/templates/components/bar-component.hbs
<h3>Hello from bar!</h3>
<div>{{post.author}}</div>
```

```app/routes/index.js
var posts = [{
    componentName: 'foo-component',  // key used to determine the rendered component
    body: "There are lots of à la carte software environments in this world."
  }, {
    componentName: 'bar-component',
    author: "Drew Crawford"
}];

export default Ember.Route.extend({
  model() {
    return posts;
  }
});
```

```app/templates/index.hbs
{{#each model as |post|}}
  {{!-- either foo-component or bar-component --}}
  {{component post.componentName post=post}}
{{/each}}
```

Para deixar mais simples, os dados do `componentName` estão escrito na mão dentro do objeto post,
mas facilmente você pode modificar para basear essas propriedades em outras coisas.

Quando o parametro passado para o `{{component}}` for igual à `null` ou `undefined`,
o helper não renderizará nada. Quando o parametro mudar, o componente atual será destruido e um novo componente será
criado e colocado em seu lugar.

Escolher diferentes componentes para renderizar em resposta à dados permite que você 
tenha um template e um comportamento para cada caso. O helper `{{component}}` é 
um ferramenta poderosa para melhorar a modularidade do seu codigo.
