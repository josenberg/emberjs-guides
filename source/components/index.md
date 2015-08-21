HTML foi desenvolvido em uma epoca que o navegador era um simples leitor de 
documentos. Desenvolvedores começaram a construir grandes web apps e precisavam de algo a mais.

No entendo, em vez de tentar substituir HTML, o Ember.js se integra com ele e adiciona 
novas features que o modernizam o jeito de contruir web apps.

Atualmente, você esta limitado as tags criadoas para você pela W3C.
Não seria muito maneiro se você pudesse criar seus proprios elementos
e atribuir comportamento para eles usando JavaScript?

Isso é exatametne oque um componente deia você fazer. 
Na verdade essa ideia é tão fantastica que atualmente a W3C 
esta trabalhando em [Elementos costumizados](https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/custom/index.html).

Para mostrar o poder dos componentes, abaixo temos um pequeno exemplo que torna post de um blog em
um elemento chamado `blog-post` que pode ser usado diversas vezes na sua aplicação, continue nessa sessão
para mais detalhes em construção de componentes.

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
